# Prerequisites #

For QuickServer v 1.2 and above

  * Recommended JVM : v1.6 or above.
  * Minimum JVM : v1.5
  * Java Logging API (any one)
  * Jakarta Commons Components {Digester, Pool}
    * This product includes software developed by the Apache Software Foundation  (http://www.apache.org/). Jar files are shipped with library along with BeanUtils, Collections, Logging http://jakarta.apache.org/commons/components.html. Apache Software License is included in the file "apache\_license.txt" for your convenience.


# Installation #
  1. Using the setup or compressed archive extract QuickServer into a directory of your choice say c:\QuickServer
  1. Add the "c:\QuickServer\dist\QuickServer.jar" file to the CLASSPATH

## Testing the installation ##

  1. Open the command prompt.
  1. Navigate to the installation folder c:\QuickServer\examples by typing command like:
```
cd c:\QuickServer\examples
  or
cd /QuickServer/examples/
```
  1. Type one of the following commands:
```
java echowebserver.EchoWebServer
or
java com.ddost.net.server.QuickServer conf\EchoWebServer.xml
```

  * It reads configuration from xml file using QuickServer.  Do not click on the EchoWebServer.bat it will work even if you don’t set the path.
  * It should display output similar to the figure 2-1 below:

```
Loading QuickServer v1.4.8 [PID:1234]... Done
17:36:06,146 [INFO   ] org.quickserver.net.server.QuickServer.initServer - Loaded init server hook: echowebserver.SetupLoggingHook
17:36:06,266 [INFO   ] org.quickserver.net.server.QuickServer.loadServerHooksClasses - Loaded server hook: echowebserver.PrepareHook

-------------------------------
 Name : EchoWebServer v 1.2
 Port : 8080
-------------------------------

QSAdminServer Started on port : 9080
QSAdmin:>_
```
#### Figure 2.1 ####

  * Open your browser to: http://127.0.0.1:8080/script?msg=Hi.  You should get the response similar to this.

![http://farm8.staticflickr.com/7195/6862071243_e755de6657_b.jpg](http://farm8.staticflickr.com/7195/6862071243_e755de6657_b.jpg)

# Code for this book #
To use this book effectively you will also need the code, you must have received it with the book, if not do download it from SVN.

```
https://quickserver.svn.sourceforge.net/svnroot/quickserver/developersguide/trunk/
```

Once downloaded (if zip file) extract it to c:\Quickserver\tutor.