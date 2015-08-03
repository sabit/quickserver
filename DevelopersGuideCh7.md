# Using Logging #

Logging is an important tool for any project. Logging help us to understand what is happening inside our projects, it also provides the auditing and debugging information.

To learn more about how to use logging (Java Logging) in general refer to the Sun web site http://docs.oracle.com/javase/6/docs/technotes/guides/logging/overview.html

QuickServer currently only support Java Logging API (java.util.logging), logging was added to java in 1.4 version, so if you are using older version you can install an alternative implementation provided by Lumberjack library http://javalogging.sourceforge.net/.

Let's go about making our sample project ‘EchoServer’ to do some logging. QuickServer by default has logging enabled. But by default it has no handler attached to it except ConsoleHandler and is set to INFO level. So whenever we close connection from an connected client to our EchoServer we can see some thing similar to this on console.

```
Feb 16, 2010 10:11:25 PM ClientHandler sendSystemMsg
INFO: Closing connection : /127.0.0.1
```

This indicates that client which was connected from 127.0.0.1 closed the connection, this message was displayed using the ClientHalder class method sendSystemMsg() method.

Let's see how we can control logging in our example project, to use java logging we must first import java.util.logging package into the class.

From the above logging, we just find that some client closed the connection or lost the connection, but how did this even come, we had not written any logging command. This happened because QuickServer uses logging internally for sendSystemMsg() method of ClientHandler class.

If you check the QuickServer documentation you find that sendSystemMsg() uses INFO by default if no level was specified. You can use sendSystemMsg() in ClientCommandHandler or Authenticator or any other class to log an event.

# Simple Logging #

Now lets log every client’s IP address when a client connects to out EchoServer, open and edit `EchoCommandHandler.java` and add the following line to `gotConnected(ClientHandler handler)` function.

```
handler.sendSystemMsg("New Client : " + handler.getSocket().getInetAddress().getHostAddress(), Level.INFO);
```

You will have to import the following package to enable you to compile.
```
import java.util.logging.*;
```

Now compile and run your modified program, now try to connect you will now see that it will log your IP address when a client connects.

Now lets make QuickServer to send logs to a file (in XML format), bellow is the modified file.

```
package echoserver;

import org.quickserver.net.*;
import org.quickserver.net.server.*;

import java.io.*;
import java.util.logging.*;

public class EchoServer {
  public static void main(String s[])  {
    String cmd  = "echoserver.EchoCommandHandler";
    String auth = "echoserver.EchoServerQuickAuthenticator";
    String data = "echoserver.EchoServerPoolableData"; //Poolable

    QuickServer myServer = new QuickServer();
    
    //setup logger to log to file
    Logger logger = null;
    FileHandler xmlLog = null;
    File log = new File("./log/");
    if(!log.canRead())
      log.mkdir();
    try  {
      logger = Logger.getLogger(""); //get root logger
      logger.setLevel(Level.INFO);
      xmlLog = new FileHandler("log/EchoServer.xml");
      logger.addHandler(xmlLog);
    } catch(IOException e){
      System.err.println("Could not create xmlLog FileHandler : "+e);
    }
    //set logging level to fine
    myServer.setConsoleLoggingLevel(Level.INFO);

    myServer.setClientCommandHandler(cmd);
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

In the above code, we check if log directory is present in the location from were it was run from, if the directory can’t be read, it will create it. Then we try to get the root logger and then we add a new FileHanlder, here we have not specified the formatter since default formatter is XMLFormatter. Then we set the console logging level to INFO.

Bellow is the modified code for EchoCommandHandler.java
```
// EchoCommandHandler.java 
package echoserver;

import java.net.*;
import java.io.*;
import org.quickserver.net.server.ClientCommandHandler;
import org.quickserver.net.server.ClientHandler;
import java.util.logging.*;

public class EchoCommandHandler implements ClientCommandHandler {

  public void gotConnected(ClientHandler handler)
      throws SocketTimeoutException, IOException {
    handler.sendSystemMsg("New Client : "+
      handler.getSocket().getInetAddress().getHostAddress(),
      Level.INFO);
    handler.sendClientMsg("+++++++++++++++++++++++++++++++");
    handler.sendClientMsg("| Welcome to EchoServer v 1.0 |");
    handler.sendClientMsg("|  Note: Password = Username  |");
    handler.sendClientMsg("|        Send 'Quit' to exit  |");
    handler.sendClientMsg("+++++++++++++++++++++++++++++++");
  }
  public void lostConnection(ClientHandler handler) 
      throws IOException {
    handler.sendSystemMsg("Connection lost : " + handler.getSocket().getInetAddress());
  }
  public void closingConnection(ClientHandler handler) 
      throws IOException {
    handler.sendSystemMsg("Closing connection : " + handler.getSocket().getInetAddress());
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
        (String)handler.getServer().getStoreObjects()[0]+ "%");
    } else if(command.equalsIgnoreCase("hello")) {
       EchoServerData data = (EchoServerData) handler.getClientData();
       data.setHelloCount(data.getHelloCount()+1);
       if(data.getHelloCount()==1) {
         handler.sendClientMsg("Hello "+data.getUsername());
       } else {
         handler.sendClientMsg("You told Hello "+data.getHelloCount()+ " times. ");
       }
      } else {
      handler.sendClientMsg("Echo : "+command);
    }
  }
}
```

Now compile and run your modified program, now try to connect you will now see that it will log your IP address on console and to the xml file.

Lets set the level of the logging to FINEST, using the QSAdminGUI and then observe the change in logging content, this will increase the text being logged. One can also change logging level by modifying the code and letting level to the handler or you can also use QuickServer’s method setLoggingLevel() to set logging level of all handler.

# Advanced Logging #

When you set logging level to FINEST it logs a lot of information, one might want to separate logs of QuickServer and application. Bellow is a code that will allow you to do so.

```
package echoserver;

import org.quickserver.net.*;
import org.quickserver.net.server.*;

import java.io.*;
import java.util.logging.*;

public class EchoServer {
  public static void main(String s[])  {
    
    String cmd  = "echoserver.EchoCommandHandler";
    String auth = "echoserver.EchoServerQuickAuthenticator";
    String data = "echoserver.EchoServerPoolableData"; //Poolable

    QuickServer myServer = new QuickServer();
    
    //setup logger to log to file
    Logger logger = null;
    FileHandler xmlLog = null;
    FileHandler txtLog = null;
    File log = new File("./log/");
    if(!log.canRead())
      log.mkdir();
    try  {
      logger = Logger.getLogger("org.quickserver.net"); //get QS logger
      logger.setLevel(Level.FINEST);
      xmlLog = new FileHandler("log/EchoServer.xml");
      logger.addHandler(xmlLog);

      logger = Logger.getLogger("echoserver"); //get App logger
      logger.setLevel(Level.FINEST);
      txtLog = new FileHandler("log/EchoServer.txt");
      txtLog.setFormatter(new SimpleFormatter());
      logger.addHandler(txtLog);

      myServer.setAppLogger(logger); //imp : Sets logger to be used for app.
    } catch(IOException e){
      System.err.println("Could not create xmlLog FileHandler : "+e);
    }
    //set logging level to fine
    myServer.setConsoleLoggingLevel(Level.INFO);


    myServer.setClientCommandHandler(cmd);
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

The comments in the code should be self explanatory, Now compile and run your modified program, now try to connect you will now see that it will log QuickServer internal logging to the xml file and application level logging to txt file has we wanted.

To open the xml file you will need the logging dtd, a copy of which can be found under log directory of examples folder.


**Important**

Always try to minimise the amount of logging on the console and for detailed logging use file handler and setting its level to a low value.  This will improve the performance of your application. Avoid using System.out.println() and try to use logging instead. In ClientHandler there is a very useful method sendSystemMsg() that will logs and string passed to it .