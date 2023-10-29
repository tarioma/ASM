## Сервер
```
#include <ws2tcpip.h>
#include <winsock2.h>
#include <iostream>

#pragma comment(lib, "ws2_32.lib")

using namespace std;

const wchar_t* IP = L"192.168.1.40";
const int PORT = 55555;
const int BUFFER_SIZE = 4096;
string suffix = "$$$$";

int main() {
	SOCKET acceptSocket;
	WSADATA wsaData;
	WORD wVersionRequested = MAKEWORD(2, 2);
	int wsaerr = WSAStartup(wVersionRequested, &wsaData);

	if (wsaerr != 0)
	{
		cout << "The Winsock dll not found" << endl;
		return 1;
	}

	SOCKET serverSocket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
	
	sockaddr_in service;
	service.sin_family = AF_INET;
	InetPton(AF_INET, IP, &service.sin_addr.s_addr);
	service.sin_port = htons(PORT);

	if (bind(serverSocket, (SOCKADDR*)&service, sizeof(service)) == SOCKET_ERROR)
	{
		cout << "Bind failed: " << WSAGetLastError();
		closesocket(serverSocket);
		WSACleanup();
		return 1;
	}

	if (listen(serverSocket, 1) == SOCKET_ERROR)
	{
		cout << "Error listening on socket";
		WSACleanup();
		return 1;
	}

	cout << "Waiting for connections..." << endl;

	while (true) {
		acceptSocket = accept(serverSocket, NULL, NULL);
		if (acceptSocket == INVALID_SOCKET) {
			cout << "Accept failed: " << WSAGetLastError();
			WSACleanup();
			return 1;
		}

		sockaddr_in clientAddr;
		int clientAddrLen = sizeof(clientAddr);
		getpeername(acceptSocket, (sockaddr*)&clientAddr, &clientAddrLen);

		char clientIP[INET_ADDRSTRLEN];
		inet_ntop(AF_INET, &clientAddr.sin_addr, clientIP, INET_ADDRSTRLEN);

		char receiveBuffer[BUFFER_SIZE] = "";
		int byteCount = recv(acceptSocket, receiveBuffer, BUFFER_SIZE, 0);

		if (byteCount < 0) {
			cout << "Client error: " << WSAGetLastError();
			return 1;
		}

		cout << clientIP << "\t" << receiveBuffer << endl;
	}

	WSACleanup();

	return 0;
}
```

## Клиент
import socket

ip_address = "192.168.1.40"
port = 55555

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect((ip_address, port))

message = "Hello, Server"
client_socket.send(message.encode())

data = client_socket.recv(1024)
print("Получено от сервера:", data.decode())

client_socket.close()
