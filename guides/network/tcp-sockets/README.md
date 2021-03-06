# TCP sockets

TCP is a connection-oriented protocol.

> Learn more about <a class="fusion-button button-flat button-round button-xsmall button-default button-2" href="http://en.wikipedia.org/wiki/Transmission_Control_Protocol"><i class="fa fa-book button-icon-left"></i><span class="fusion-button-text">TCP on Wikipedia</span></a>

TCP sockets in user code are represented by objects of the class `TcpSocket`. User code can define a handler for retrieving data and detecting disconnects on a socket by using `Handle.Tcp` method:

```cs
static void Handle.Tcp(UInt16 port, Action<TcpSocket tcpSocket, Byte[] data> handler);
```

* `port`: local port on which incoming TCP connections should be accepted.
* `tcpSocket`: active socket object. This object can be used to send data, disconnect socket, etc. When incoming `data` is `null` it means that socket was disconnected.
* `data`: incoming data on TCP socket.

`TcpSocket` class has the following notable methods:

* `UInt64 ToUInt64()`: identifier that represents the TCP socket, normally used for storage in user code.
* `void Disconnect()`: disconnects an active TCP socket.
* `void Send(Byte[] data)`: sends given data on active TCP socket.
* `Boolean IsDead()`: checks if TCP socket object is still active.
* `static TcpSocket Current`: currently active TCP socket for this scheduler.

<strong>NOTE:</strong> When doing operations on the same TcpSocket but from <strong>different</strong> Starcounter schedulers - the order in which operations (like `Send`) will actually be performed is not guaranteed to be the same as the order in which they were initiated.

Once the TCP socket object is returned, user can fetch the ID representing this socket (ToUInt64()), and of course, perform data sends and disconnect.

In the following example we process TCP sockects on port 8585 and send echo data back. The example shows how socket ID can be used to associate socket with resources, for example objects in database:

```cs
Handle.Tcp(8585, (TcpSocket tcpSocket, Byte[] incomingData) =>
{
	UInt64 socketId = tcpSocket.ToUInt64();
	// Checking if we have socket disconnect here.
	if (null == incomingData)
	{
		// One can use "socketId" to dispose resources associated with this socket.
		return;
	}

	// One can check if there are any resources associated with "socketId" and otherwise create them.
	// Db.SQL("...");

	// Sending the echo back.
	tcpSocket.Send(incomingData);

  // Or even more data, if its needed: tcpSocket.Send(someMoreData);
});
```
