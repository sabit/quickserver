# Major Features #
  * Create multi-client TCP server applications.
  * Support for secure server creations: SSL, TLS.
  * Support for thread per client (multi-threaded) - Blocking Mode.
  * Support for non-blocking input output - Non-Blocking Mode.
  * Clear separation of server, protocol and authentication logic.
  * Remote administration support: QSAdminServer (With support plugable application commands).
  * Command Shell for local administration of server.
  * GUI based remote administration: QsAdminGUI (with support for plug-ins)
  * Restart or Suspend the server without killing connected clients.
  * In build pools for reusing of Threads and most used Objects.
  * Full logging support [built in Logging](Java.md).
  * Support for sending and receiving Strings, Bytes, Binary and serialized java objects.
  * Support for identifying and searching a client.
  * Support for xml configuration with ability to store application specific data in the same xml. [Sample](View.md)
  * Support for xml Based JDBC Mapping.
  * Support for Service Configurator pattern.
  * Support for restricting access to server by ip address.
  * Support for loading/reloading application jar from xml.
  * Ability to add process hooks into QuickServer.
  * Ability to specify maximum number of clients allowed.
  * Support for negotiating secure connection over normal tcp connection.
  * Nice easy examples come with the distribution - FTPServer, CmdServer, EchoWebServer, ChatServer, SecureEchoWebServer, XmlAdder, PipeServer, Filesrv.



# What's New in 2.0.0 #

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

**Download Page**: [DownloadPage](http://www.quickserver.org/download.html)

**View Change Log**: ChangeLog