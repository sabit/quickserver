# Adding Authentication #

In the last chapter we created a basic server. Now lets add some authentication to it. If we look in the [APIDocumentation](APIDocumentation.md) [folder](docs.md) of org.quickserver.net.server.QuickServer you will notice it has a method like

```
public void setAuthenticator(java.lang.String authenticator)
```

Further reading of the [APIDocumentation](APIDocumentation.md) tells us that authenticator is the fully qualified name of the class that implements

`org.quickserver.net.server.Authenticator` class.

There are two implementation of Authenticator class, namely

`org.quickserver.net.server.QuickAuthenticator`
This class is used to authenticate a client when it connects to QuickServer. Only single instance of this class will be used per QuickServer to handle all authentications. (Recommended implementation).

`org.quickserver.net.server.ServerAuthenticator`
This class is used to authenticate a client when it connects to QuickServer Every client connected will have separate instance of this class which will be used to handle the authentication.


Lets click on the QuickAuthenticator it shows us a simple example of how we can write our own authenticator class for QuickServer, so lets us write one for our EchoServer.

So how will our authenticator authenticate our client, to keep it simple, client who supplies a password that is same as the username is authenticated.


  1. Go to the directory created in the previous chapter [c:\projects\java\echoserver](say.md).
  1. Now lets create the authenticator class of echoserver ‘EchoServerQuickAuthenticator’ save it in the directory ‘echoserver’

```
package echoserver;

import org.quickserver.net.server.*;
import java.io.*;

public class EchoServerQuickAuthenticator extends QuickAuthenticator {

  public boolean askAuthorisation(ClientHandler clientHandler) 
      throws IOException {    
    String username = askStringInput(clientHandler, "User Name :");
    String password = askStringInput(clientHandler, "Password :");

    if(username==null || password ==null)
      return false;
    
    if(username.equals(password)) {
      sendString(clientHandler, "Auth OK");
      return true;
    } else {
      sendString(clientHandler, "Auth Failed");
      return false;
    }
  }
} 
```

#### Listing 4.1 ####

This class extends the `org.quickserver.net.server.QuickAuthenticator` class.

In the method askAuthorisation() we ask username from the client by using askStringInput() method inherited from QuickAuthenticator class.

The same method askStringInput(), will read the client input. If the password matches the username supplied, we return true indicating client was authorised else we return false.

Now lets tell QuickServer to use our new authenticator as its Authenticator. Edit the java file EchoServer.java created in the previous chapter to look like this [setAuthenticator() method].

```
package echoserver;

import com.ddost.net.*;
import com.ddost.net.server.*;

import java.io.*;

public class EchoServer {

  public static void main(String s[])  {
    
    QuickServer myServer = 
      new QuickServer("echoserver.EchoCommandHandler");
    myServer.setAuthenticator(
      "echoserver.EchoServerQuickAuthenticator");
    myServer.setPort(4123);
    myServer.setName("EchoServer v 1.0");
    try {
      myServer.startServer();
    } catch(AppException e){
      System.err.println("Error in server : "+e);
    }
  }
}
```

Now lets compile our changed program. Run it as described the previous chapter. When you try to connect this time it will ask you for a username and password. If you supply user name equals to the password it will log you in If you try wrong password for more that five times it will disconnect the client printing ‘-ERR Max Auth Try Reached’.

This can be changed using setMaxAuthTry() and setMaxAuthTryMsg() method of QuickServer class.  Bellow is a sample output.

```
R: ++++++++++++++++++++++++++++++++++++++
R: | 	Welcome to EchoServer v 1.0 	| 
R: |        Send 'Quit' to exit  	| 
R: ++++++++++++++++++++++++++++++++++++++
R: User Name : 
S: user 
R: Password : 
S: user 
R: Auth OK 
S: Hello 
R: Echo : Hello 
```

Some time when authorising we may need to close the client connection instead of letting him try again we can do this in two ways

**Throw a AppException from askAuthorisation() method**
Like this
```
	String username = askStringInput(clientHandler, "User Name :");
	if(username!=null && username.equalsIgnoreCase("QUIT")) {
		sendString(clientHandler, "Logged out.");
		throw new AppException("Quit");
	}
```

**Get reference to ClientHandler and then close the connection.**
Like this
```
	String username = askStringInput(clientHandler, "User Name :");
	if(username!=null && username.equalsIgnoreCase("QUIT")) {
		sendString(clientHandler, "Logged out.");
		ClientHandler ch = getClientHandler();
		ch.closeConnection();
		return false;
	}
```

_Note: ClientHandler object can provide many useful information about client connected like ip address, refer to the API docs for more information._

**Warning**
  * Do not store any client related information in the authenticator class, if you need you must put it in the ClientData class – covered in the next chapter.
  * You mast also make sure the askAuthorisation()  method is thread safe