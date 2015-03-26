title: Document
date: 2015-03-26 05:25:20
tags:
---

Servit is based on [Node.js](https://nodejs.org), which uses an event-driven, non-blocking I/O model. So the game running on the server just need to handle serval events to transmit data to server and send to every client.

The `Client` class is used as the client of the game and transmitting data between server and client. You can simply create a new connection by creating an instance of `Client`.

### Properties
Serval properties are used for storing the server and game data in the client-side. Besides, you could also create other properties for storing custom data in your client.

### gameState : number
The state of  the game. It defined the current state of the game and the states could be customs by user. Here is some examples.

```
game.states = {
	"startState" : 0,
	"waitState"  : 1,
	"mapState" 	 : 2,
	"gameState"  : 3
}
```

### clientInfo : object
The information about the client, which could be used by the server.

### socket : socket.io#http
The connection between client and server for transmitting data.

### clients : object
The information of all the online clients. The property name is the `clientID` and the value is `clientInfo`.

### clientIP : string
the IP address of the client.

### host : string
The IP address of the host client.

### hostRight : string
The special right of the host client.

### Events 
The client need to listen to these events for receiving data from the server. Also, some of the events need to be emitted to server from clients.

#### Client events
##### getConnected
Once the client is connected to the server, the server would emit `getConnected` event to this client. Here is the structure of message:

```
{
"accept": boolean, // whether the client is accepted by the server
"totalClients": number, // the online client number
"host": string, // the IP of the host
"clientID": string, // the ID of the client
"clientIP": string, // the IP of the client
}
```

#### accept
This event is emitted by client after it receive the `getConnected` from server. It would send the information of it self to the server.
#### changeState
The client could change the state of the game by emit `changeState` to server. Once the server received the event and the data, it would change the state of the game and emit `changeState` event to every clients. Here is the structure of the message:

```
{
"state": number // the state of  the game
}
```
#### gameOperate
The client emit `gameOperate` event for interact with other client. The data about the action and some custom data of the action would be sent to the server. The server then emit the action to all the client. Here is the structure of the message:
```
{
"action": string, // the name of the action, which would be used as event name to emit to all the client
... // some data about this action
}
```
### getInfo
Client could emit this event to get the information of the server and the client. Here is the structure of the message that the server send back to client:

```
{
"accept": boolean, // whether the client is accepted by the server
"totalClients": number, // the online client number
"host": string, // the IP of the host
"clientID": string, // the ID of the client
"clientIP": string, // the IP of the client
}
```

#### update
`update` event is emitted by client to server for updating the client information and announce all other clients. Here is the structure of the message:

```
{
"clientID": string, // the ID of the client
"obj": msg.obj,
"clients": clients
};
```
#### serverOpen, serverClose
These two events could be emitted by host client to control the state of server. `serverClose` would stop the server from accepting new client and `serverOpen` would reopen it.

#### disconnect
`disconnect` is emitted once the client stop the connection between server. The server would remove the client information from `clients` and announce all the client about this.

#### accepted
The server listen to `accept` event and once received, the server would save the client information in `clients`and emit the `accepted`event to client . Here is the structure of the message:

```
{
"clients": object // the information of all the online clients 
}
```
#### disconnection
`disconnection` is emitted after the server receive the `disconnect` event. This would send the disconnected client information to all the other clients. If the disconnected client is the last client of the server, it would reset the server. Here is the structure of the message:
```
{
"host": string, // the IP of the host
"clientID": string, // the ID of the client
"clientIP": string, // the IP of the client
}
```