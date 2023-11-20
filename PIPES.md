## Server
```cpp
#include <windows.h>
#include <iostream>
#include <thread>
#include <vector>
#include <sstream>
#include "Pipes.h"

using namespace std;

vector<HANDLE> receivers;

void receive_process(HANDLE hPipe)
{
	thread::id threadId = this_thread::get_id();
	stringstream ss;
	ss << threadId;
	string userId = ss.str();

	while (true)
	{
		time_t currentTime = time(nullptr);
		char buffer[26];
		ctime_s(buffer, sizeof(buffer), &currentTime);

		string s = GetString(hPipe);
		string message = "user_" + userId + '\t' + to_string(currentTime) + '\t' + s;
		cout << message << endl;

		for (auto& receiver : receivers)
		{
			SendString(receiver, message);
		}

		auto lastError = GetLastError();
		if (lastError == ERROR_NO_DATA || lastError == ERROR_BROKEN_PIPE)
		{
			DisconnectNamedPipe(hPipe);
			CloseHandle(hPipe);
			return;
		}
	}
}

void receive_handler()
{
	while (true)
	{
		HANDLE hPipe = CreateNamedPipe("\\\\.\\pipe\\receive",
			PIPE_ACCESS_DUPLEX, PIPE_TYPE_MESSAGE | PIPE_READMODE_MESSAGE | PIPE_WAIT, PIPE_UNLIMITED_INSTANCES,
			1024, 1024, 0, NULL);

		ConnectNamedPipe(hPipe, NULL);
		thread t(receive_process, hPipe);
		t.detach();
	}
}

void send_handler()
{
	while (true)
	{
		HANDLE hPipe = CreateNamedPipe("\\\\.\\pipe\\send",
			PIPE_ACCESS_DUPLEX, PIPE_TYPE_MESSAGE | PIPE_READMODE_MESSAGE | PIPE_WAIT, PIPE_UNLIMITED_INSTANCES,
			1024, 1024, 0, NULL);

		ConnectNamedPipe(hPipe, NULL);
		receivers.push_back(hPipe);
	}
}

int main()
{
	cout << "Server" << endl;

	thread receiver(receive_handler);
	thread sender(send_handler);

	receiver.join();
	sender.join();
}
```

## Client
```cpp
#include <windows.h>
#include <iostream>
#include <conio.h>
#include <thread>
#include <chrono>
#include <ctime>
#include <iomanip>
#include <string>
#include "../PipesServer/Pipes.h"

using namespace std;

void send_messages()
{
	if (WaitNamedPipe("\\\\.\\pipe\\receive", 3000))
	{
		HANDLE hPipe = CreateFile("\\\\.\\pipe\\receive", GENERIC_READ | GENERIC_WRITE, 0, NULL, OPEN_EXISTING, 0, NULL);
		while (true)
		{
			string s;
			cin >> s;
			SendString(hPipe, s);
		}
		CloseHandle(hPipe);
	}
	else
	{
		cout << "No pipes available" << endl;
	}
}

void receive_messages()
{
	if (WaitNamedPipe("\\\\.\\pipe\\send", 3000))
	{
		HANDLE hPipe = CreateFile("\\\\.\\pipe\\send", GENERIC_READ | GENERIC_WRITE, 0, NULL, OPEN_EXISTING, 0, NULL);
		while (true)
		{
			string message = GetString(hPipe);
			if (message.empty())
			{
				continue;
			}
			cout << message << endl;
		}
		CloseHandle(hPipe);
	}
	else
	{
		cout << "No pipes available" << endl;
	}
}

int main()
{
	thread receiver(receive_messages);
	thread sender(send_messages);

	receiver.join();
	sender.join();
}
```
