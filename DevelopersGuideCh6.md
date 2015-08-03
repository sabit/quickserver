# Using Remote Admin #

Our server EchoServer, works fine. Lets say we need to modify few server configuration like Timeout message or Max Auth try message or max times authorization try can be done per session (maxAuthTry).. may be remotely, this is supported by QuickServer.

We need an admin server that will control our server todo the above mentioned task has well as much other control function without changing server code or even shutdown the server when that is running.

This server is implemented by QuickServer library has another QuickServer implementation called

```
org.quickserver.net.qsadmin.QSAdminServer
```

To use this feature all we have to do is call `startQSAdminServer()` method of `QuickServer` object. The default port on which `QSAdminServer` will run on is 9876, this can be changed by using any of the two methods
```
quickserver.setQSAdminServerPort(4124);
//or
quickserver.getQSAdminServer().getServer().setPort(4124);
```

Bellow is the new code that has enabled QSAdminServer for EchoServer on port 4124.

```
//EchoServer .java
package echoserver;

import org.quickserver.net.*;
import org.quickserver.net.server.*;

import java.io.*;

public class EchoServer {
  public static void main(String s[])  {
    
    String cmd  = "echoserver.EchoCommandHandler";
    String auth = "echoserver.EchoServerQuickAuthenticator";
    String data = "echoserver.EchoServerPoolableData"; //Poolable

    QuickServer myServer = new QuickServer(cmd);
    myServer.setAuthenticator(auth); 
    myServer.setClientData(data);

    myServer.setPort(4123);
    myServer.setName("Echo Server v 1.0");

    //config QSAdminServer 
    myServer.setQSAdminServerPort(4124);
    myServer.getQSAdminServer().getServer().setName("EchoAdmin v 1.0");
    try  {
      myServer.startQSAdminServer();
      myServer.startServer();
    } catch(AppException e){
      System.out.println("Error in server : "+e);
    }
  }
}
```

So we have defined our QSAdminServer to run on 4124 port.. but what will the Authentication used?

If no explicit Authenticator is set then QSAdminServer will use `org.quickserver.net.qsadmin.Authenticator` has its Authenticator. If we check the [APIDocumentation](APIDocumentation.md) we find that is a very simple, is username and password is hard coded has

```
Username : Admin
Password : QsAdm1n 
```

So, now lets compile the new program and run it, it should show the output has bellow indicating admin server is running on 4124 port.

```
Loading QuickServer v1.4.8 [PID:4748]... Done

-------------------------------
 Name : EchoAdmin v 1.0
 Port : 4124
-------------------------------


-------------------------------
 Name : Echo Server v 1.0
 Port : 4123
-------------------------------
```

Start the QSAdmin GUI using following command
```
java -jar QSAdminGUI.jar
```

On Windows you can also do the following:
Clicking on batch file “QsAdminGUI.bat” located under bin directory of QuickServer installation.

On Linux/Unix you can also do the following:
Clicking/Executing on batch file “QsAdminGUI.sh” located under bin directory of QuickServer installation.

The GUI should be some thing like this
![http://farm8.staticflickr.com/7047/6887045799_82f767c299_o.png](http://farm8.staticflickr.com/7047/6887045799_82f767c299_o.png)

You can now connect to the Admin server of the EchoServer by clicking on “Login” button on the top right hand side. This will popup an login dialog box where you can input IP Address and port were our server is running like 127.0.0.1 and 4124.

It will also ask you the username and password, since we are using the default authenticator the default will work for us. Then click on login button.

![http://farm8.staticflickr.com/7210/6887045899_bca2c7a0d3_o.png](http://farm8.staticflickr.com/7210/6887045899_bca2c7a0d3_o.png)

Once logged in the Status will show Authorised, now you can send it command by selecting the target server and clicking on the command buttons on the main tab.

So what’s target? Target is basically the server to which the command has to be sent, since both EchoServer(Server) and our EchoAdmin (AdminServer) are basically `QuickServers`.  The command buttons have nice descriptive tool tips that tell what the command will do.

On the right had side there is a text box that can be used to send any commands directly to the server, refer to the QsAdmin command handler: `org.quickserver.net.qsadmin.CommandHandler` api docs.

To change server configuration you can use “Get/Set” tab. Just select the target and click on “Reload Properties For The Target” to load the configuration for the target server. Change the parameter you like and click on “Save” button to save it. Some parameter will need the server to be restarted for it to take effect (though this will not kill any connected client).

Just try a few options and explore the GUI.

# Adding your own command #
Say we want to change some property of the server when its running with out changing code or restarting server, we can do this by adding your own command to admin server.

To demonstrate this feature lets take hypnotically example. Say, we will display current interest rate offered when user send "What's interest?" This can be stored in a db and changed if needed with out restarting server, but lets say it was stored has a variable and we need to change it when the server was running.

Now lets implement the command handler to display the interest, bellow is the code.

Lets first change our echoserver and command handler and display interest.

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
    String data = "echoserver.EchoServerPoolableData"; //Poolable

    QuickServer myServer = new QuickServer(cmd);
    myServer.setAuthenticator(auth); 
    myServer.setClientData(data);

    myServer.setPort(4123);
    myServer.setName("Echo Server v 1.0");

    //store data needed to be changed by QSAdminServer
    Object[] store = new Object[]{"12.00"};
    myServer.setStoreObjects(store);

    //config QSAdminServer 
    myServer.setQSAdminServerPort(4124);
    myServer.getQSAdminServer().getServer().setName("EchoAdmin v 1.0");
    try  {
      myServer.startQSAdminServer();
      myServer.startServer();
    } catch(AppException e){
      System.out.println("Error in server : "+e);
    } 
  }
}
```

```
//EchoCommandHandler.java 
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
      return;
    }
    if(command.equals("What's interest?")) {
      handler.sendClientMsg("Interest is : "+
        (String)handler.getServer().getStoreObjects()[0]+
        "%");
    } else if(command.equalsIgnoreCase("hello")) {
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

Now let's define our CommandPlugin for QSAdminServer.
```
//QSAdminCommandPlugin.java
package echoserver;

import java.io.*;
import java.net.SocketTimeoutException;
import org.quickserver.net.server.*;
import org.quickserver.net.qsadmin.*;

public class QSAdminCommandPlugin implements CommandPlugin {
  /**
   * Echoserver commands
   * ----------------------------------
   * set interest value
   * get interest
   */
  public boolean handleCommand(ClientHandler handler,  String command)
    throws SocketTimeoutException, IOException {

    QuickServer echoserver = (QuickServer) 
      handler.getServer().getStoreObjects()[0];
    Object obj[] = echoserver.getStoreObjects();

    if(command.toLowerCase().startsWith("set interest ")) {
      String temp = "";
      temp = command.substring("set interest ".length());
      obj[0] = temp;
      echoserver.setStoreObjects(obj);
      handler.sendClientMsg("+OK interest changed");
      return true;
    } else if(command.toLowerCase().equals("get interest")) {
      handler.sendClientMsg("+OK " + (String)obj[0]);
      return true;
    }
    //ask QSAdminServer to process the command
    return false;
  }
}
```

In the above code we get the reference to the QuickServer of EchoServer from the store objects of QSAdminServer [Ref. QuickServer [APIDocumentation](APIDocumentation.md) for QSAdminServer and CommandPlugin], using the code

```
QuickServer echoserver = (QuickServer) 
			handler.getServer().getStoreObjects()[0];
```

Then we return true if processed the command got, else we return false indicating the QSAdminServer’s default command handler should be used to handle the command. The same technique can be used to override the default command of QSAdminServer’s command handler.

Now let's tell QuickServer to use this class has its QSAdminServer’s command plugin. Bellow is the code.

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
    String data = "echoserver.EchoServerPoolableData"; //Poolable

    QuickServer myServer = new QuickServer(cmd);
    myServer.setAuthenticator(auth); 
    myServer.setClientData(data);

    myServer.setPort(4123);
    myServer.setName("Echo Server v 1.0");

    //store data needed to be changed by QSAdminServer
    Object[] store = new Object[]{"12.00"};
    myServer.setStoreObjects(store);

    //config QSAdminServer 
    myServer.setQSAdminServerPort(4124);
    myServer.getQSAdminServer().getServer().setName("EchoAdmin v 1.0");
    try  {
      //add command plugin
      myServer.getQSAdminServer().setCommandPlugin(
        "echoserver.QSAdminCommandPlugin");
      myServer.startQSAdminServer();
      myServer.startServer();
    } catch(AppException e){
      System.out.println("Error in server : "+e);
    } catch(Exception e){
      System.out.println("Error : "+e);
    }
  }
}
```

So, now let's compile the new program and run it. Now start a client like SocketTest and send the command "**What's interest?**” it should display "**Interest is : 12.00%**". Now lets start QSAdminGUI has told earlier. And type the following command from Send message box and hit send.

`get interest` This should now show the following output

`+OK 12.00` Now lets change it, send the following command

`set interest 15.00`

Now again start a client like SocketTest and send the command "**What's interest?**” it should display "**Interest is : 15.00%**".

So try a few tricks and experiment and learn.