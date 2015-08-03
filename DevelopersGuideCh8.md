# XML Configuration #

In the previous chapters as we have developed the EchoServer, we have been configuring the QuickServer aspects in the actual class.  While this is an acceptable practice in some situations, may applications prefer to allow users to dynamically configure the application at start-up.  To do this for the QuickServer portion, you can tell the server instance to read a XML file.

To configure QuickServer all you have to do is to first write the configuration file and then tell QuickServer object to load its configuration form it. Bellow is a very simple configuration file.

```
<quickserver>
	<name>EchoServer v 1.0</name>
	<client-command-handler>
		echoserver.EchoCommandHandler
	</client-command-handler>
 </quickserver>
```

Now there are two ways to start your server,

  * Using QuickServer startup parameter – load

In this all you have to do is put QuickServer.jar and the all your classes to the classpath and start QuickServer by specifying the path of the xml configuration file using “–load” switch. Eg:

```
java –jar QuickServer.jar –load myxmlconfig.xml
                  or
java org.quickserver.net.server.QuickServer -load myxmlconfig.xml
                  or
quickserver.bat -load  myxmlconfig.xml
```

  * Using QuickServer initService() method

Some time you may need to start your application form you own jar file or class file or may want to add few code before server starts then this option is the best suited. Bellow is the code to initialise QuickServer from a xml configuration file.

```
QuickServer myServer = new QuickServer();
//pick the xml file form config folder
String confFile = "conf" + File.separator + "MyServer.xml";
Object config[] = new Object[] {confFile};
if(myServer.initService(config) != true) { 
   System.err.println("Could’t init server !!");
}
```

So now let's write the configuration file for our EchoServer that we wrote in previous chapter. Bellow is the XML file.

The XML configuration that is shipped with the examples is below
```
<quickserver>
  <name>EchoServer v 1.0</name>
  <port>4123</port>
  <bind-address>127.0.0.1</bind-address>
  
  <client-command-handler>
    echoserver.EchoCommandHandler
  </client-command-handler>
  <authenticator>
    echoserver.EchoServerQuickAuthenticator
  </authenticator>
  <client-data>
    echoserver.EchoServerPoolableData
  </client-data>
  
  <console-logging-level>INFO</console-logging-level>

  <!-- some extra config. added just to show -->
  <timeout>4</timeout>
  <timeout-msg>-ERR Timeout</timeout-msg>
  <max-auth-try>5</max-auth-try>
  <max-auth-try-msg>-ERR Max Auth Try Reached</max-auth-try-msg>
  <max-connection>-1</max-connection>
  <max-connection-msg>
    Server Busy\nMax Connection Reached
  </max-connection-msg>
  <object-pool>
    <max-active>-1</max-active>
    <max-idle>15</max-idle>
  </object-pool>
  <!-- some extra config. added just to show -->

  <qsadmin-server>
    <name>EchoAdmin v 1.0</name>
    <port>4124</port>
    <bind-address>127.0.0.1</bind-address>
    <command-plugin>
      echoserver.QSAdminCommandPlugin
    </command-plugin>
  </qsadmin-server>

</quickserver> 
```

Bellow is the modified EchoServer.java file; it is now changed to load configuration from the xml file.

```
package echoserver;

import org.quickserver.net.*;
import org.quickserver.net.server.*;

import java.io.*;
import java.util.logging.*;

public class EchoServer {
  public static void main(String s[])  {
    
    QuickServer myServer = new QuickServer();
    
    //setup logger to log to file
    Logger logger = null;
    FileHandler xmlLog = null;
    FileHandler txtLog = null;
    File log = new File("./log/");
    if(!log.canRead())
      log.mkdir();
    try  {
      logger = Logger.getLogger("org.quickserver.net"); //get qs logger
      logger.setLevel(Level.FINEST);
      xmlLog = new FileHandler("log/EchoServer.xml");
      logger.addHandler(xmlLog);

      logger = Logger.getLogger("echoserver"); //get app logger
      logger.setLevel(Level.FINEST);
      txtLog = new FileHandler("log/EchoServer.txt");
      txtLog.setFormatter(new SimpleFormatter());
      logger.addHandler(txtLog);
      //img : Sets logger to be used for app.
      myServer.setAppLogger(logger); 
    } catch(IOException e){
      System.err.println("Could not create xmlLog FileHandler : "+e);
    }

    //store data needed to be changed by QSAdminServer
    Object[] store = new Object[]{"12.00"};
    myServer.setStoreObjects(store);

    //load QuickServer from xml
    String confFile = "config"+File.separator+"EchoServer.xml";
    Object config[] = new Object[] {confFile};
    if(myServer.initService(config) == true) {
      try  {
        myServer.startQSAdminServer();
        myServer.startServer();
      } catch(AppException e){
        System.out.println("Error in server : "+e);
      } catch(Exception e){
        System.out.println("Error : "+e);
      }
    }
  }
}
```

To learn more about how to use XML configuration refer to QuickServer Java Documentation (main page has a sample xml) and to the example that come with QuickServer.