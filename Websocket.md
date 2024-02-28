# Websocket: How it Works and How it Differs from HTTP
This page explains the usefulness of Websocket, as well as when it is preferred to HTTP.

## What is Websocket

A Websocket is a connection between a server and client that allows for communication to be initiated both ways. This allows a server to broadcast different notifications to clients without requiring the client to send a request first. Websocket is an upgrade added to regular HTTP (Hypertext Transfer Protocol); HTTP allows a server to respond to client requests, and along with HTTPS (a secure version of HTTP) make up most of the connections between your web browser and most websites.

## How Websocket Works

When adding Websocket functionality to a server we open a pathway for clients to reach with the following line of code: `Spark.webSocket("/connect",WebSocketHandler.class);`. This allows the client to make the connection to the server for live updates and notifications as shown in Figure 1. When this connection is live the client can send messages to the server and the server is able to send messages to a single client or to multiple across the network. The "onMessage" function must be overridden so that the client knows what to do when messages are received from the server. In the chess example in Figure 1 when a client receives a message it can be one of three types; Depending on the type the client displays either an error a notification or refreshes the game to show new moves.

#### Client side Websocket example
```
public WebSocketClient(String uri, ChessGame.TeamColor color, Game game) throws Exception {
        this.color = color;
        newGame = game;
        uri = uri.replace("http", "ws");
        URI socketURI = new URI(uri + "/connect");
        WebSocketContainer container = ContainerProvider.getWebSocketContainer();
        this.session = container.connectToServer(this, socketURI);
        this.session.addMessageHandler(new MessageHandler.Whole<String>() {
            @Override
            public void onMessage(String message) {
                ServerMessage sMessage = new Gson().fromJson(message,ServerMessage.class);
                System.out.println(SET_TEXT_COLOR_MAGENTA + "Recieved Message...");
                switch (sMessage.getServerMessageType()) {
                    case NOTIFICATION -> {
                        NotificationServerMessage notify = new Gson().fromJson(message, NotificationServerMessage.class);
                        System.out.print(notify.getMessage());
                    }
                    case LOAD_GAME -> {
                        LoadGameServerMessage gameMess = new Gson().fromJson(message, LoadGameServerMessage.class);
                        newGame =  gameMess.getGame();
                        printGame(gameMess.getGame(), color);
                    }
                    case ERROR -> {
                        ErrorServerMessage error = new Gson().fromJson(message, ErrorServerMessage.class);
                        System.out.print(error.getErrorMessage());
                    }
                }
            }
        });
    }
```
 In this example a Websocket connection is established with a server, after which when a message is received it is parsed into one of three categories and handled accordingly. *Figure 1* [1]

## Why Should Websocket be Used
HTTP communication is useful in situations where users don’t need to connect with each other, and only need to receive information when it is requested. An example of this is When you are on a web browser and click on a link, this sends a request from your machine to a server, the server then retrieves and gives you the website that is at the end of the link. You only need to go to a website if you first request it. This is less useful when you want to connect with other users on a server or receive live updates and notifications. There were many shortcuts created to solve this, but none were very efficient.

Websocket protocols were built in 2011 in an effort to make it easier to do this kind of live communication. In HTTP a server is unable to reach any of the clients that are connected but with Websocket a list of active connections allows the server to know who is connected and reach out to those users to deliver messages. The server is then able to receive a message from one user, say to login, and convert that into a message to tell others that that user is active. Since the server can initiate communication network traffic is reduced and each client does not have to periodically reach out to the server to receive those messages. A server is also able to make calls to different APIs (Application Programming interface, used to communicate with other applications) to gain more information to provide users. see Figure 2

#### Websocket Commpared to HTTP
![Websocket vs. HTTP communnication figure 1](https://ambassadorpatryk.com/wp-content/uploads/2020/02/web-socket.png) 

HTTP connections require the client to make a request before sending information, while Websocket can send information whenever it deems necessary is available. *Figure 2* [2]

## Conclusion

Websocket is an efficient way to facilitate communication between different users and servers. If your application requires the server to dynamically deliver information to multiple users as it receives that information then Websocket should be used, as it frees up your computer ports and is able to initiate communication which eliminates the need for more complex code in your clients.

### Acknowledgement

1. Figure 1 https://github.com/kobydj/ChessProjectkobydj/blob/master/client/src/starter/WebSocketClient/WebSocketClient.java
2. Figure 2 https://ambassadorpatryk.com/wp-content/uploads/2020/02/web-socket.png
