# Introduction #
`QuickServer` is a free, open source Java library for quick creation of robust and multi-threaded, multi-client TCP server applications. It provides an abstraction over the java.net.ServerSocket class; it eases the creation of powerful server applications. It has been designed and implemented by Akshathkumar Shetty.

[Example](Examples.md) programs demonstrating the use of the library can be found with the QuickServer distribution  [examples](Examples.md) folder.

Latest examples, documentation is also available through the website http://www.quickserver.org or http://quickserver.sourceforge.net

This guide is for any developer who wants to learn and use QuickServer. You should have basic knowledge of coding in java and a basic knowledge of networking and sockets will help.

# Need for QuickServer #
In any programming language, socket programming is no small feat to any programmer, and creating a multi-threaded multi-client server socket is a nightmare for most programmers.

We always waste time in writing the same code each time we build new software, which handles multiple socket connections. So I have made a library - QuickServer, to handle a creation of multi-threaded, multi-client server applications for Java. With this library one can just concentrate on just the application logic/protocol on hand.

# Basic Architecture #
QuickServer divides the application logic of its developer over eight main classes,

  * ClientEventHandler [Class](Optional.md)
    * Handles client events
  * ClientCommandHandler
    * Handle client interaction - Character/String data.
  * ClientBinaryHandler
    * Handles client interaction - Binary data.
  * ClientObjectHandler
    * Handles client interaction - Java Object data.
  * ClientWriteHandler [Class](Optional.md)
    * Handles client write operation - Non-Blocking Mode Only.
  * ClientAuthenticationHandler [Class](Optional.md)
    * Used to authenticate a client.
  * ClientData [Class](Optional.md)
    * Client data carrier (support class)
  * ClientExtendedEventHandler [Class](Optional.md)
    * Handles extended client events.

The diagram below shows the basic architecture of QuickServer library. The seven spokes on the QuickServer block are the seven Service methods.

![http://farm8.staticflickr.com/7209/6861846329_6df7af1abe_o.gif](http://farm8.staticflickr.com/7209/6861846329_6df7af1abe_o.gif)
#### Figure 1.1 ####

  * java.lang.String info()
  * int getServiceState()
  * boolean initService(java.lang.Object[.md](.md) config)
  * boolean startService()
  * boolean resumeService()
  * boolean suspendService()
  * boolean stopService()

Of the eight components connected to QuickServer block only ClientCommandHandler class is the absolutely necessary class.

QuickServerConfig object is constructed by initService() method of the Service interface, that QuickServer implements, after reading configuration from XML file. QuickServerConfig is then used to configure QuickServer.

ClientHandler thread object is used from the pool of object for every client connected; optional ClientData class is associated with the ClientHandler class. ClientHandler object contain references to ClientCommandHandler, ClientObjectHandler, ClientAuthenticationHandler (optional) objects contained with in the main QuickServer.

NOTE: QSAdminServer is not shown in this diagram, it is a composed QuickServer within the main QuickServer.

See the [Architecture](Architecture.md) for more on architecture of QuickServer.

# Major Features #
  * Create multi-threaded, multi-client TCP server applications.
  * Support for secure server creations: SSL, TLS.
  * Clear separation of server, protocol and authentication logic.
  * Remote administration support with GUI.
  * Restart or Suspend the server without killing connected clients.
  * In build pools for reusing of Threads and most used Objects.
  * Full logging support [built in Logging](Java.md).
  * Support for sending and receiving Strings.
  * Support for sending and receiving Bytes.
  * Support for sending and receiving serialized java objects.
  * Support for xml configuration.
  * Support for xml Based JDBC Mapping
  * Support for Service Configurator pattern.
  * Specify maximum number of clients allowed.
  * Easy methods to identify and search any specific client.
  * Nice easy examples come with the distribution - FTPServer, CmdServer, EchoWebServer, ChatServer.


More on Features may be viewed at [Features](Features.md)

# Request to Developers #
If you would like to contribute to the development of QuickServer please do get in touch with me. I am always on the lookout for people who can contribute to make this library even better.

If you use QuickServer in your development and if you would like to share your experience with the QuickServer community, please feel free to email me the details or do post it in the QuickServer Forums. If possible do mail across the source code of your project. If found interesting your project will be showcased on the powered projects section of QuickServer’s homepage. Thanks.

# Credits #
Thanks to everyone who helped me with this book. Thanks to all users who sent their valuable comments and suggestion. My special thanks to Mr. Sean Stallbaum for all his contributions. My special thanks to www.amgmedia.com for the coffee pot image.