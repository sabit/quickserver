# Building Basic EchoServer #

A good way to learn how to use QuickServer library is to study the examples provided with it. And even better way is to writing a server using the QuickServer library. That’s what we will be doing in the rest of this guide. Though the server that we build is not that useful, it will demonstrate all the features that QuickServer library provides you with. We start by building a basic server and then keep on adding a new feature in each chapter.

# Coding #

So what is the server we will be building? It is called EchoServer – It’s a simple TCP server that echoes back any line of input that user sends with and '`Echo : `' prefixed.

Create a directory at some location [say c:\projects\java\ ](.md) called ‘echoserver’.

Now lets create the main class of echoserver **EchoServer.java** save it in the directory ‘echoserver’.

```
//EchoServer.java
package echoserver;

import org.quickserver.net.*;
import org.quickserver.net.server.*;

import java.io.*;

public class EchoServer {
  public static void main(String s[])  {
    QuickServer myServer = 
      new QuickServer("echoserver.EchoCommandHandler");
    myServer.setPort(4123);
    myServer.setName("EchoServer v 1.0");
    try {
      myServer.startServer();
    } catch(AppException e){
      System.err.println("Error in server : "+e);
    }
  }
}
```

When we define the QuickServer object myServer we pass it a String object that says which class the QuickServer must load and use as its command handler for any client.

This class is a user created class that implements org.quickserver.net.server.ClientCommandHandler interface.

Next, we set the port that the server will listen on and then set the Application’s name "EchoServer v 1.0".

Finally, we actually start the server.

Now lets create the command handler class for our echoserver, that handles commands sent to echo server,  **EchoCommandHandler.java** save it in the directory ‘echoserver’.

```
// EchoCommandHandler.java 
package echoserver;

import java.net.*;
import java.io.*;
import org.quickserver.net.server.ClientCommandHandler;
import org.quickserver.net.server.ClientHandler;

public class EchoCommandHandler implements ClientCommandHandler {

  public void gotConnected(ClientHandler handler)
      throws SocketTimeoutException, IOException {
    handler.sendClientMsg("+++++++++++++++++++++++++++++++");
    handler.sendClientMsg("| Welcome to EchoServer v 1.3 |");
    handler.sendClientMsg("|        Send 'Quit' to exit  |");
    handler.sendClientMsg("+++++++++++++++++++++++++++++++");
  }
  public void lostConnection(ClientHandler handler) 
      throws IOException {
    handler.sendSystemMsg("Connection lost : " + 
      handler.getSocket().getInetAddress());
  }
  public void closingConnection(ClientHandler handler) 
      throws IOException {
    handler.sendSystemMsg("Closing connection : " + 
      handler.getSocket().getInetAddress());
  }

  public void handleCommand(ClientHandler handler, String command)
      throws SocketTimeoutException, IOException {
    if(command.equals("Quit")) {
      handler.sendClientMsg("Bye ;-)");
      handler.closeConnection();
    } else {
      handler.sendClientMsg("Echo : "+command);
    }
  }
} 
```

This class implements the org.quickserver.net.server.ClientCommandHandler interface as required by QuickServer.

When client gets connected, gotConnected() function of the ClientCommandHandler is called, so in this method we send some welcome text to the client.

The text is sent the client using the sendClientMsg() method of the passed ClientHandler object.

We also display [log](log.md) the INetAddress of the connected client to the consol using sendSystemMessage() method of the passed ClientHandler object.

The handlerCommand() method is the focal method in the ClientCommandHandler interface because this method is called every time server receives any line of input from the client.

In our implementation, we check to see if the sent command is equal to ‘Quit’ and if it is then we send some text back to user indicating server will close connection and close the connection, else we send same text back to the user with ‘Echo : ‘ prefixed to it.

# Running and Testing #

Now lets compile our new program...
  * Go to command prompt (cmd.exe)
  * Traverse to the base directory:
> > ` cd c:\projects\java `
  * Compile the code
> > ` javac echoserver\*.java `
  * Run the server [no errors when compiling](if.md)
> > ` java echoserver.EchoServer `
  * You should get something like this
```
Loading QuickServer v1.4.8 [PID:1234]... Done

-------------------------------
 Name : EchoServer v 1.0
 Port : 4123
-------------------------------
```

#### Figure 3.1 ####

Testing to see if our server is working, as we want it to. For this step we can use any socket communication software like telnet. But I like SocketTest – a java swings base tool.

Download it from http://sockettest.sourceforge.net/

Start the socket communication program [double click on sockettest.jar file or from the command prompt type the following command
`java –jar sockettest.jar`

In the open window connect to the localhost on port 4123.

For SocketTest
  * Enter IP as 127.0.0.1
  * Port as 1234
  * Click on ‘Connect’ button.

For telnet use command:
  * open localhost 4123

Ones connection is made you should see a banner like the figure bellow.

![http://farm8.staticflickr.com/7065/6862167413_df8ae6fcf9_o.png](http://farm8.staticflickr.com/7065/6862167413_df8ae6fcf9_o.png)
#### Figure 3.2 ####

Try sending some strings to the server and see what it send back. Send ‘Quit’ to disconnect from the server.