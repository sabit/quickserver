# Introduction #
This Quick Start Guide shows you how to create a multi client "HelloWorld" server application using the QuickServer (v 1.4.6 or above).

## Interaction ##
So lets say our server will interact with the client some thing like this..
```
C: <open connection>
S: Hello Word!
C: hi
S: You Sent: Hi
C: quit
S: bye
S: <close connection>
```


# Code #
So now lets start by writing a command handler that implements this

```
// HelloWorldCommandHandler.java
package mypackage;

import java.net.*;
import java.io.*;
import org.quickserver.net.server.ClientCommandHandler;
import org.quickserver.net.server.ClientHandler;

public class HelloWorldCommandHandler implements ClientCommandHandler {

	public void handleCommand(ClientHandler handler, String command) 
			throws SocketTimeoutException, IOException {
		if(command.equals("quit")) {
			handler.sendClientMsg("bye");
			handler.closeConnection();
		} else {
			handler.sendClientMsg("You Sent: "+command);
		}
	}
}
```

This class implements the org.quickserver.net.server.ClientCommandHandler interface as required by QuickServer. Rest of the code should be self explanatory (else refer to "Developers Guide").

Now compile this file and make a jar out of it. Make sure you include QuickServer.jar in your CLASSPATH environment variable before you try to compile.

```
javac mypackage/*.java
jar cvf helloworld.jar mypackage/*.class
```

Now we need to write our xml file to tell QuickServer about our server's configuration. Note that writing xml is not required and QuickServer can be started with out one (configurable programmatically), but it just makes it easy to use xml and configure a server. So below is the HelloWorld.xml

```
<quickserver>
	<name>HelloWorld v 0.1</name>
	<port>1000</port>
	<bind-address>0.0.0.0</bind-address>

	<client-command-handler>
		mypackage.HelloWorldCommandHandler
	</client-command-handler>

	<application-jar-path>helloworld.jar</application-jar-path>
</quickserver>
```

So that's it, we should be ready with our server. Lets start it by giving the following command.

```
java -jar QuickServer.jar -load HelloWorld.xml
```

You should see similar output on your screen.

![http://quickserver.org/docs/quickstart/output1.gif](http://quickserver.org/docs/quickstart/output1.gif)

## Testing ##
Now you can use any socket communication software like telnet to connect on port 1000 and comunicate with the server that we just built.

Alternatively you may use 'SocketTest' - a java based tool for testing sockets (Download/WebStart from http://sockettest.sourceforge.net/)

Below is the screen shot of a simple test using SocketTest

![http://quickserver.org/docs/quickstart/test1.jpg](http://quickserver.org/docs/quickstart/test1.jpg)

## Client Code ##
Also below is a code for a simple java client for our HelloWorld server.
```
// HelloWorldClient.java
package mypackage;

import java.net.*;
import java.io.*;

public class HelloWorldClient {
	public static void main(String args[]) {
		BufferedReader br = null;
		PrintWriter out = null;
		Socket socket = null;
		int port = 1000;

		if(args.length<2) {
			System.err.println("Usage : "+
			"\n HelloWorldClient  ");
			System.exit(0);
		}

		try {
			port = Integer.parseInt(args[1]);
			System.out.print("Connecting.. ");

			socket = new Socket(args[0], port);
			System.out.println("Connected to "+
				args[0]+":"+port+"\n");

			br = new BufferedReader(new InputStreamReader(
				socket.getInputStream()));
			out = new PrintWriter(new BufferedWriter(
				new OutputStreamWriter(
					socket.getOutputStream())), true);

			String recData = br.readLine();
			System.out.println("S: "+recData);

			out.println("Hi");
			System.out.println("C: Hi");

			recData = br.readLine();
			System.out.println("S: "+recData);

			out.println("quit");
			System.out.println("C: quit");
			recData = br.readLine();
			System.out.println("S: "+recData);

		} catch(Exception e) {
			System.err.println("Error " + e);
		} finally {
			try {
				if(socket!=null)
					socket.close();
			} catch(Exception er) {
				System.err.println("Error closing: " + er);
			}
		}
	}
}
```

Note that in the above QuickStart we receive and send strings that end with newline (\r\n) character. This is known as STRING mode and is the default mode of data exchange. QuickServer supports other modes of data exchange like BYTE, BINARY, OBJECT.


If you like to know about features that come with QuickServer [click here](Features.md).


For more information on architecture view ```QuickServer``` [Basic-Architecture](Architecture.md).

If you need more detailed explanation on developing server's using QuickServer refer to examples that come with QuickServer.

Its also recommended you reffer [Developers Guide](DevelopersGuide.md) and [API Documentation](APIDocumentation.md).