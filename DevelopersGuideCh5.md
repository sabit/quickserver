# Using ClientData #

There have been a lot of references to the ClientData class in the previous chapters, though if we examine the QuickServer class's [APIDocumentation](APIDocumentation.md) it has no method in it.

Since the ClientCommandHandler and ServerAuthenticator class can’t hold any client data we use ClientData class to store any client information between function calls to handleCommand() or askAuthorisation().

To demonstrate how this is a useful feature you need to know, lets say our EchoServer will greet the user with his username if he sends ‘Hello’ to it.

And if user sends ‘Hello’ more than ones it will replay to him telling him he has told Hello n times including this Hello.

So lets define out ClientData class to store the username and count of hello told to the server by the client.

  * Go to the directory created in the Chapter 3 [c:\projects\java\echoserver](say.md).
  * Now lets create the ClientData class of echoserver ‘EchoServerData.java’ save it in the directory ‘echoserver’

```
//EchoServerData.java
package echoserver;

import org.quickserver.net.server.*;
import java.io.*;

public class EchoServerData implements ClientData {
  private int helloCount;
  private String username;

  public void setHelloCount(int count) {
     helloCount = count;
  }
  public int getHelloCount() {
     return helloCount;
  }

  public void setUsername(String username) {
     this.username = username;
  }
  public String getUsername() {
     return username;
  }
}
```
#### Listing 5.1 ####

Now lets tell QuickServer to use our EchoServerData as its ClientData class.

Edit the java file EchoServer.java created in the previous chapter to look like this [for setClientData() method](look.md).

```
//EchoServer.java
package echoserver;

import org.quickserver.net.*;
import org.quickserver.net.server.*;

import java.io.*;

public class EchoServer {
  public static void main(String s[])  {
    
    String cmd  = "echoserver.EchoCommandHandler";
    String auth = "echoserver.EchoServerQuickAuthenticator";
    String data = "echoserver.EchoServerData";

    QuickServer myServer = new QuickServer(cmd);
    myServer.setAuthenticator(auth); 
    myServer.setClientData(data);

    myServer.setPort(4123);
    myServer.setName("Echo Server v 1.0");
    try  {
      myServer.startServer();
    } catch(AppException e){
      System.out.println("Error in server : "+e);
    }
  }
}
```
#### Listing 5.2 ####

In the above we have put our configuration information into string variables and then used these variable to set them into a QuickServer object.

  * Now we need to modify our Authenticator class i.e., EchoServerAuthenticator class so that it stores the username in the ClientData object. Bellow is the modified code:

```
//EchoServerAuthenticator .java
package echoserver;

import org.quickserver.net.server.*;
import java.io.*;

public class EchoServerQuickAuthenticator extends QuickAuthenticator {

  public boolean askAuthorisation(ClientHandler clientHandler) 
      throws IOException {    
    String username = askStringInput(clientHandler, "User Name :");
    if(username!=null && username.equalsIgnoreCase("QUIT")) {
      sendString(clientHandler, "Logged out.");
      //close the connection
      clientHandler.closeConnection();
      return false;
    }

    String password = askStringInput(clientHandler, "Password :");

    if(username==null || password ==null)
      return false;
    
    if(username.equals(password)) {
      sendString(clientHandler, "Auth OK");
      //store the username in ClientData
      EchoServerData data = (EchoServerData) clientHandler.getClientData();
      data.setUsername(username);
      return true;
    } else {
      sendString(clientHandler, "Auth Failed");
      return false;
    }
  }
} 
```
#### Listing 5.3 ####

  * Now we need to modify our ClientCommandHandler implementation class i.e., EchoCommandHandler.java so that it greets the user with his username if he sends ‘Hello’ to it. And if user sends ‘Hello’ more than ones it will replay to him telling him he has told Hello n times including this Hello.

Bellow is the modified code

```
// EchoCommandHandler.java 
package echoserver;

import java.net.*;
import java.io.*;
import org.quickserver.net.server.ClientCommandHandler;
import org.quickserver.net.server.ClientHandler;

public class EchoCommandHandler implements ClientCommandHandler {

  public void gotConnected(ClientHandler handler)
      throws SocketTimeoutException, IOException {
    handler.sendClientMsg("+++++++++++++++++++++++++++++++");
    handler.sendClientMsg("| Welcome to EchoServer v 1.0 |");
    handler.sendClientMsg("|  Note: Password = Username  |");
    handler.sendClientMsg("|        Send 'Quit' to exit  |");
    handler.sendClientMsg("+++++++++++++++++++++++++++++++");
  }
  public void lostConnection(ClientHandler handler) 
      throws IOException {
    handler.sendSystemMsg("Connection lost : " + 
      handler.getSocket().getInetAddress());
  }
  public void closingConnection(ClientHandler handler) 
      throws IOException {
    handler.sendSystemMsg("Closing connection : " + 
      handler.getSocket().getInetAddress());
  }

  public void handleCommand(ClientHandler handler, String command)
      throws SocketTimeoutException, IOException {
    if(command.equals("Quit")) {
      handler.sendClientMsg("Bye ;-)");
      handler.closeConnection();
    } if(command.equalsIgnoreCase("hello")) {
       EchoServerData data = (EchoServerData) handler.getClientData();
       data.setHelloCount(data.getHelloCount()+1);
       if(data.getHelloCount()==1) {
         handler.sendClientMsg("Hello "+data.getUsername());
       } else {
         handler.sendClientMsg("You told Hello "+data.getHelloCount()+
         " times. ");
       }
      } else {
      handler.sendClientMsg("Echo : "+command);
    }
  }
} 
```
#### Listing 5.4 ####

  * Now lets compile our changed program.
  * Run it as described the previous chapters.
  * When you try to connect this time it will ask you for username and password. If you supply a user name equal to the password it will log you in.
  * If you enter ‘Hello’ it will greet you with your username and you send hello again it will tell how many times you sent hello to it. Bellow is a sample output.

```
S: ++++++++++++++++++++++++++++++++++
S: | 	Welcome to EchoServer v 1.0 	|
S: |  Note: Password = Username  	|
S: |    Send 'Quit' to exit  		|
S: ++++++++++++++++++++++++++++++++++
S: User Name :
C: Sharath
S: Password :
C: Sharath
S: Auth OK
C: Hello World
S: Echo : Hello World
C: Hello
S: Hello Sharath
C: Hello
S: You told Hello 2 times. 
C: quit
S: Echo : quit
C: Quit
S: Bye ;-)
```

# Making ClientData Poolable #

The code that we have written till now that uses ClientData works fine, but the problem is that for every client connected QuickServer will create a new object of ClientData. This can be performance bottleneck, if the ClientData was in turn having heavy objects part of it.

**You should only pool ClientData if the object is heavy or is expensive to create.**

This problem can be slowed by telling QuickServer that it has to, create a pool of objects for ClientData and use objects from this pool whenever a client connects to it. We can do this implementing the interface
```
org.quickserver.util.pool.PoolableObject
```
If we look into QuickServer [APIDocumentation](APIDocumentation.md) we find that PoolableObject has only two methods that must be implemented.

  * `getPoolableObjectFactory()` which returns an object of `org.apache.commons.pool.PoolableObjectFactory` which belongs to Commons Pool library.
  * `isPoolable()` which returns if Object is poolable or not (boolean)


## Basics of PoolableObjectFactory ##
Interface `org.apache.commons.pool.PoolableObjectFactory` contains following method that need to be implemented

  * void activateObject(Object obj)
    * Re-initialise an instance to be returned by the pool.
  * void destroyObject(Object obj)
    * Destroys an instance no longer needed by the pool.
  * Object makeObject()
    * Creates an instance that can be returned by the pool.
  * void passivateObject(Object obj)
    * Un-initialise an instance to be returned to the pool.
  * boolean validateObject(Object obj)
    * Ensures that the instance is safe to be returned by the pool.

There is also a basic no-operation implementation that we can extend to make out object poolable this class is `org.apache.commons.pool.BasePoolableObjectFactory` this class only has makeObject() method has abstract and the `validateObject()` function will always return true.

So let's write the code so that our ClientData class is also poolable, let it be EchoServerPoolableData, bellow is the code.

```
//EchoServerPoolableData.java
package echoserver;

import org.quickserver.net.server.*;
import java.io.*;

public class EchoServerPoolableData 
    extends EchoServerData 
    implements org.apache.commons.pool.PoolableObjectFactory  {
   
  public void activateObject(Object obj) {
  }
  public void destroyObject(Object obj) {
    if(obj==null) return;
      passivateObject(obj);
      obj = null;
  }  
  public Object makeObject() {
    return new EchoServerPoolableData();
  }
  public void passivateObject(Object obj) {
    EchoServerPoolableData pd = (EchoServerPoolableData)obj;
    pd.setHelloCount(0);
    pd.setUsername(null);
  }
  public boolean validateObject(Object obj) {
    if(obj==null) 
      return false;
    else
      return true;
  }
}
```
#### Listing 5.5 ####

The above code just extends our old ClientData and then we implement org.apache.commons.pool.BasePoolableObjectFactory. The implementation is simple so no explanation should be needed.

Now we need to tell QuickServer to use this class instead of the old data class. Bellow is the code.

```
package echoserver;

import org.quickserver.net.*;
import org.quickserver.net.server.*;

import java.io.*;

public class EchoServer {
  public static void main(String s[])  {
    
    String cmd  = "echoserver.EchoCommandHandler";
    String auth = "echoserver.EchoServerQuickAuthenticator";
    //String data = "echoserver.EchoServerData"; //Non-Poolable
    String data = "echoserver.EchoServerPoolableData"; //Poolable

    QuickServer myServer = new QuickServer(cmd);
    myServer.setAuthenticator(auth); 
    myServer.setClientData(data);

    myServer.setPort(4123);
    myServer.setName("Echo Server v 1.0");
    try  {
      myServer.startServer();
    } catch(AppException e){
      System.out.println("Error in server : "+e);
    }
  }
}
```
#### Listing 5.6 ####

Now lets compile our changed program. This will, most likely will return an error saying
```
package org.apache.commons.pool does not exist
```

This is because compiler does not know this class, you will have to add the
commons-pool.jar in QuickServer library installation directory (Say c:\QuickServer).

This can be done by the following command on windows
```
set CLASSPATH=%CLASSPATH%;c:\QuickServer\dist\commons-pool.jar
```

This can be done by the following command on Unix/Linux
```
CLASSPATH=$CLASSPATH:/home/user/QuickServer/dist/commons-pool.jar
```

Run it as described the previous chapters. When you try to connect this time, the output of the server will be the same but ClientData class is not recreated for each client connected instead it will be reused.