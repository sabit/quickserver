# Introduction #
QuickServer is an open source Java library/framework for quick creation of robust multi-client TCP server applications. QuickServer provides an abstraction over the ServerSocket, Socket and other network and input output classes and it eases the creation of powerful network servers.

Example programs demonstrating the use of the library can be found with the QuickServer distribution [folder](examples.md).

Homepage: http://www.quickserver.org/


# Need for QuickServer #

In any programming language, socket programming is not small fleet to most programmer and creating a multi-threaded multi-client network server is a nightmare for most programmers. We always waste time in writing the same code each time we build a new software which acts has network server (handles multiple socket connections). I always create such application, so this time I have made a library - QuickServer, to handle a creation of a multi-threaded, multi-client server applications for Java. With this library one can just concentrate on just the application logic/protocol on hand.


# Brief Architecture #
QuickServer divides the application logic of its developer over eight main classes,

  * ClientEventHandler [Class](Optional.md) - Handles client events.
  * ClientCommandHandler [#](#.md) - Handles client interaction - Character/String commands.
  * ClientBinaryHandler [#](#.md) - Handles client interaction - Binary data.
  * ClientObjectHandler [#](#.md) - Handles client interaction - Java Object commands.
  * ClientWriteHandler [Class](Optional.md) - Handles client write operation - Non-Blocking Mode.
  * ClientAuthenticationHandler [Class](Optional.md) - Used to Authenticate a client.
  * ClientData [Class](Optional.md) - Client data carrier (support class)
  * ClientExtendedEventHandler [Class](Optional.md) - Handles extended client events.

[#](#.md) = Any one of these have to be set based on default DataMode for input. The default DataMode for input is String so if not changes you will have to set ClientCommandHandler.

For more information on architecture view QuickServer [Basic-Architecture](Architecture.md).

For information on features view Feature List

Developers do checkout our [QuickStart-Tutor](QuickStartTutor.md), [Developers Guide](DevelopersGuide.md).

## API Documentation ##
View API Docs of v [1.4.7](http://quickserver.org/docs/1.4.7)
View API Docs of v [2.0.0](http://quickserver.org/docs/2.0.0)


## Request to Developers ##
If you would like to contribute to the development of QuickServer please do get in touch with us. We are always on the lookout for people who can contribute to make this library even better.

If you use QuickServer in your development and if you would like to share your experience with the QuickServer community, please feel free to post it in the [QuickServer-Forums](http://groups.google.com/group/quickserver). Thanks.


## Want to support QuickServer? ##
Was this library useful for you? If so, please help support QuickServer development. There is a number of ways to support, see more details [here](http://quickserver.org/support.html).