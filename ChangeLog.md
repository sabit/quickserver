## Version 2.0.0 [15-Feb-2014] ##
  * Added new commands in QSAdmin
    * kill-client-with target search\_term
    * kill-clients-all target
  * Added readBytes with length support for BlockingClient
  * Added PID, Lib Version, Uptime in info server
  * Added QuickServerDebugHelper class - allows info server and pool dump to log file
  * Bug fix in BlockingClientPool (high tps noop failure fix)
  * Bug fix in SocketMonitor
  * Bug fix in readCRLFLine for BlockingClient
  * QSAdmin New commands introduced like JVM jmap & jstack

## Version 2.0.0 RC1 [20-Oct-2012] ##
  * Added BroadcastServer example.
  * Added org.quickserver.net.client
  * Added org.quickserver.net.client.loaddistribution.
  * Added org.quickserver.net.client.monitoring
  * Added org.quickserver.util.pool.thread.app a thread pool for use by application logic
  * Added more param in advanced-settings for performance-preferences
  * Added Generic Client Socket Pool
  * Added TotalReadBytes, TotalWrittenBytes to client handler
  * Added support for raw Communication Logging (in case of binary mode)
  * Added BlockingUDBClient and UDP Monitoring support
  * Added AsyncHandler for JDK logging
  * sendClientXXX methods improved to make synchronized writes.
  * Improved TextFile read methods.
  * Improved readInputStream() method in BasicClientHandler to check for available data and EOF in read() method call.

## Version 1.4.7 [01-Mar-2006] ##
  * Implemented support NIO writes in sendClientXXX methods of ClientHandler.
  * Added initServer() that throws App Exception if it fails.
  * Added getServiceError() to Service interface.
  * Added load() method to QuickServer.
  * Removed synchronized from processGotDataInBuffers in NonBlockingClientHandler.
  * Improved ByteBufferInputStream for readLine
  * Added availableOnlyInByteBuffer() in NonBlockingClientHandler.
  * Improved isLineReadyForStringBuffer() in NonBlockingClientHandler.
  * Improved isClientEventNext() in BasicClientHandler.
  * Added check for NewLine miss in NonBlockingClientHandler.
  * Added flush() in closeConnection() in BlockingClientHandler.
  * Improved setClassLoader() of QuickServer.
  * Added check for bootstrap classloader (null) in getClass() method of QuickServer
  * Closed Keystore stream when no longer needed.
  * shutdown command in QSAdminServer improved to close shell, this will make server auto exit the jvm when the last client disconnects.
  * GhostSocketReaper now will close pools after last client disconnects after server is stopped.
  * Added checks for null returned in ServerSocketChannel accept method.
  * Fixed run NonBlockingClientHandler code to prevent more than one threads from processing selector when server is re-started.
  * Modified registerChannel to add to a list and then process when selector becomes free.
  * Fixed synchronization problem in ClientThread pool.returnObject() call.
  * MaxThreadAccessCount in NonBlockingClientHandler is now configurable.
  * Wakeup call on selector after RegisterForWrite and RegisterForRead in NonBlockingClientHandler is now configurable.
  * Improved validateObject method in ClientHandlerObjectFactory to check if object was created from the same pool
  * Access to clientEvents method is now synchronized.
  * Added flush call after ObjectOutputStream is created in BasicClientHandler.
  * Improved kill/exit command in QSAdmin to take a param (exit value)

## Version 1.4.6 [20-Oct-2005] ##

  * Improved ThreadPool for performance.
  * Improved BasicClientHandler.
  * Improved XmlAdderLoadTest.
  * Added byte-buffer-pool-info command - QsAdmin
  * Fixed initSize default value setting
  * Fixed synchronized blocks on clientEvents and moved clientConnectedTime code to run()
  * Improved Architecture.pdf

## Version 1.4.6 beta [17-Sep-2005] ##

  * Added initService(QuickServerConfig)
  * Added maxThreadsForNioWrite() for Thread Pools
  * Added ClientEventHandler to xml, also provided default implementation.
  * Added new QsAdmin commands 'client-handler-pool-dump'
  * Added isOpen() method to ClientHandler
  * Added ability to set default DataMode.
  * Added getBlockingMode() to QuickServer
  * Added Unix shell scripts.
  * Added ClientExtendedEventHandler.
  * Added facility to configure initial pool size.
  * Added method to BasicServerConfig so that business logic class can be set and checked at compile time.
  * Introducing new ClientAuthenticationHandler (replacing Authenticator)
  * Added new org.quickserver.util.Assertion class.
  * Added new org.quickserver.util.logging.SimpleJDKLoggingHook class
  * Improved logging, ClientHandler
  * Refactored ClientHandler
  * Moved QSAdminGUI into a seperate jar
  * Made most beans Serializable
  * Fixed ClientEvent loss bug caused when more than one event gets picked at the same time.
  * Fixed charset bug in sendClientMsg() and sendClientBytes() methods when length of byte[.md](.md) == representation is not equal to string representation
    * Removed deprecated setServerAuthenticator(), getServerAuthenticator in QuickServer
    * ServerAuthenticator and "server-authenticator" tag have been removed.
    * Old xml format of v1.2 no longer supported.
    * MaxClientHandler no longer needed, so removed.
    * ObjectCleaner, ObjectCleanup no longer used, so removed.
  * New XML Tags: advanced-settings/max-threads-for-nio-write, object-pool/init-size, object-pool/thread-object-pool/init-size, object-pool/client-handler-object-pool/init-size, object-pool/byte-buffer-object-pool/init-size, object-pool/client-data-object-pool/init-size, client-event-handler, qsadmin-server/client-event-handler default-data-mode/data-type-in,default-data-mode/data-type-out, client-extended-event-handler, qsadmin-server/client-extended-event-handler client-authentication-handler, qsadmin-server/client-authentication-handler

## Version 1.4.5 beta [May 2005](31.md) ##

  * Added support for NIO (non-blocking mode) for normal sockets: Server Mode
  * QuickServer now uses java assertion.
  * Added new QsAdmin commands to find pool information.
  * Added new QsAdmin command 'systeminfo', 'start console' and 'stop console'.
  * Backlog and Socket linger for server is now configurable.
  * Added GUI prompt for sensitive application parameter.
  * Added -fullXML2File to -load switch.
  * Added new interface ClientEventHandler, ClientWriteHandler.
  * Improved object pool handling and creation.
  * Improved QSAdminGUI to handle error in custom authenticators.
  * Communication logging is set to false by default now.
  * Improved communication logging for BINARY mode and general logging.
  * Improved org.quickserver.util.logging classes implementation.
  * Improved EchoWebServer example.
  * ClientHanlder.isConected() renamed to isConnected().
  * New XML Tags: Server Mode, advanced-settings, application-configuration/prompt-type, object-pool/thread-object-pool, object-pool/client-handler-object-pool, object-pool/byte-buffer-object-pool, object-pool/client-data-object-pool

## Version 1.4.1 [Nov 2004](22.md) ##

  * Improved ClientHandler, QuickServer, ClientThread classes.
  * Improved BasicObjectPool class.
  * Improved QSAdminShell to detect forced exits.
  * Improved FTPServer, EchoServer, XmlAdder examples.
  * Fixed bug in QSAdminShell bug did not allow prompt changing.
  * Fixed bugs in ClientPool (thread pool) that prevented excess threads from freeing up.
  * Fixed bug in prompt of sensitive 

&lt;property-value&gt;

. ! Fixed bug in for QSAdminServer from XML.


## Version 1.4.0 [Oct 2004](01.md) ##

  * Added SecureManagerLoader to manage Secure Mode.
  * Added findAllClientByKey to QuickServer object.
  * Added ConnectionLostException class.
  * Improved Secure configuration
    1. To prompt the user for store/key password if not specified.
    1. Auto pick security provider (if not given).
    1. To handle non file based keystores (pass NONE for 

&lt;store-file&gt;

) like from HSM or LDAP.
    1. Ability to override the type and provider of SecureStore in TrustStoreInfo.
  * Add ability to read sensitive 

&lt;application-configuration&gt;

 

&lt;property-value&gt;

 from console.
  * Improved ChatServer, XmlAdder examples.
  * Many bug fixes as per reports on Developers Forum

## Version 1.4.0 beta [Aug 2004](27.md) ##

  * Added Secure Mode : SSL, TLS
  * Added method to negotiate secure connection over normal tcp connection: ClientHandler.makeSecure()
  * Added method in ClientHandler to find if client is in secure mode.
  * Added Init ServerHook : org.quickserver.net.InitServerHook
  * Added BINARY mode of communication: ClientBinaryHandler
  * Added BINARY reading and writing methods to QuickAuthenticator
  * Added QSAdminAPI api for communicating to QsAdminServer.
  * New example: XmlAdder - A simple xml server that adds 2 integers.
  * New example: PipeServer - A simple redirection server.
  * Improved ClientHandler.
  * Updated jakarta commons jars.
  * Improved Architecture.pdf
  * Fixed bug in QSAdminServer loading of Plugin class from 

&lt;application-jar-path&gt;

.
  * Fixed bug in AccessConstraintConfig for QsAdminServer
  * Fixed bug in setting of max connection from xml file for QSAdminServer
    * Removed the old package structure form distribution com.ddost

## Version 1.3.3 [Jun 2004](27.md) ##

  * Added IP Filter for clients 

&lt;access-constraint&gt;



&lt;ip-filter&gt;

.
  * Added node to specify SecurityManager for JVM.
  * Loading/Reloading application jar from xml. Add 

&lt;application-jar-path&gt;

 node to specify location of jars needed by application. This location can be either absolute or relative (to config file). The jars are reloaded when server is restarted, updating the class from the changed jars.
  * Added startup, shutdown process hooks : org.quickserver.net.ServerHook
  * QuickServer is now cloneable
  * Added reaper thread : GhostSocketReaper
  * Added new method to ClientHandler to find the time when client last sent data to ClientHandler.
  * Fixed bug in communication logging turn off implementation in ClientHandler.
  * Fixed bug in BasicObjectPool return object method.

## Version 1.3.2 [May 2004](20.md) ##

  * Added plugin ability to QSAdminGUI : PluginPanel
  * Added "Server Statistics View" Plugin to QSAdminGUI
  * Added QSAdmin Shell - A command shell
  * Added 

&lt;application-configuration&gt;

 node xml configuration file to store data needed by the application.
  * Added BYTE reading and writing methods to QuickAuthenticator
  * Added ability to turn off FINE logging of all data sent in/out of server. Note: This will not log any incoming data when client is being authenticated.
  * Added two new logging formatter - SimpleTextFormatter, SimpleConsoleFormatter
  * Improved login and command == Version handling in QSAdminGUI
  * Fixed "-load" option to load QSAdmin from XML
  * Fixed ClientHandler detection of lost connections when in BYTE mode.

## Version 1.3.1 [Mar 2004](06.md) ##

  * Added BYTE mode of communication.
  * Added a new interface ClientIdentifiable and methods to identify and search a client.
  * Added new method to ClientHandler to find the time when Client was assigned to ClientHandler.
  * New example: ChatServer - Send and receive text b/w connected clients
  * Improved logging in ClientHandler to log all outgoing and incoming communications at FINE level.
  * Improved error detection for Authenticator implementations.
  * Fixed the ClientHandler Thread cleanup for detection of lost connection and connection closing condition.
  * Fixed setConsoleLoggingLevel() method from setting non console handlers level.

## Version 1.3.0 [Jan 2004](27.md) ##

  * www.quickserver.org was registered.
  * All classes ported to new package org.quickserver from com.ddost
  * Released Developer Guide v 0.2 (Pre-Release)
  * Improved Architecture.pdf.

## Version 1.3.0 beta [Jan 2004](01.md) ##

  * Object Pool {Includes a thread pool} added for handling clients.
  * Added PoolableObject interface to pool any class used by QuickServer like ClientData.
  * Added QSAdmin GUI (XML Based)
  * New authentication class added QuickAuthenticator and Authenticator.
  * New commands added to QSAdmin CommandHander
  * XML Based JDBC Mapping [org.quickserver.sql.DBPoolUtil] ==
  * QuickServer optimised for speed.
  * Authentication is now can have single or multiple instance.
  * XML Configuration of QuickServer format is now changed to a easy one. Old XML configuration file is automatically converted to new format.
  * Stack overflow bug when bind ip address was set to QSAdmin fixed.

> ## Version 1.2.0 [Sep 2003](24.md) ##

  * Released Developer Guide v 0.1 (Pre-Release)
  * XML DTD added for configuration file.
  * New ReadMe added for Examples
  * Improved Architecture.pdf.
  * Location of main jar changed from build\lib to dist folder.
  * Improved Ant build file.
  * setMaxAuthTryMag() method changed to setMaxAuthTryMsg()

## Version 1.2.0 beta [Aug 2003](20.md) ##

  * JDK Logging support added to QuickServer.
  * Support for sending and receive java objects in QuickServer.
  * Support for xml configuration.
  * QuickServer now supports Service Configurator Pattern.
  * New example: DateServer - Send and receive java objects.
  * FtpServer example modified to use txt logging.
  * EchoServer example modified to use xml configuration and logging.
  * EchoWebServer example modified to use txt logging and xml configuration.
  * Multiple ObjectCleaner bug when QuickServer is restarted rectified.
  * Cleanup time setting bug in ObjectCleaner rectified.
  * ObjectCleaner thread is a daemon thread now.
  * Pluggable command handler for QSAdminServer - CommandPlugin
  * FTPServer example: Custom command added to QSAdminServer for remote changing of ftp root - using CommandPlugin.

## Version 1.1.0 [Aug 2003](04.md) ##

  * JUnit tests added to QuickServer
  * New example : FTPServer - A basic ftp server.

## Version 1.1.0 beta [Jul 2003](14.md) ##

  * QuickServer.setMaxClients() added.
  * QuickServer.setBindAddr() added.
  * Many new useful methods added to QuickServer.
  * Admin port & protocol added to QuickServer [org.quickserver.net.qsadmin] ==
  * ClientHandler.getClientCount(), ClientHandler.getObjectCount() bug when more than one QuickServer objects created in same JVM rectified.
  * EchoServer example modified to use QSAdminServer.
  * Few new useful methods added to ClientHandler.
  * New example: CmdServer - A remote command shell

## Version 1.0.0 [Jul 2003](10.md) ##

  * Improved QuickServer.startServer() to detect failure.
  * Improved Main Thread of QuickServer for performance.

## Version 1.0.0 beta [Jul 2003](07.md) ##

  * First public release