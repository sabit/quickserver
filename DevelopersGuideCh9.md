# Data Modes and Data Types #

Until now we have been communicating only using string that end with `<CR><LF>`. Well this will work most Internet standard protocols. But some time we may have to be able to receive stream of character bytes or receive java objects.

This is, were the DataMode and DataType classes comes in handy. Using this you can tell ClientHandler which mode of communication to be used.

# DataMode #

DataMode class is used to define format of data exchange between QuickServer and client socket. There are currently four supported modes

  * **`DataMode.STRING`** – This is the default mode of exchange. In this mode you can receive data as String (characters terminated by `<CR><LF>` or `\r\n`).

  * **`DataMode.BYTE`** – In this mode you can receive all the char sent by the client including `<CR>` or `<LF>` or any other control characters. This mode is very useful when dealing with hardware based client or non-standard protocol like XML over http or when defining your own protocol.

  * **`DataMode.BINARY`** – In this mode you can receive all the binary data sent by the client including `<CR>` or `<LF>` or any other control characters. This mode is very useful when dealing with hardware based client or non-standard protocol like XML over http or when defining your own protocol.

  * **`DataMode.OBJECT`** – In this mode you can receive java objects that are Serializable. This mode is only useful if the client is also written in Java.


# DataType #

DataType class is used to define the type (direction) of data exchanging between QuickServer and client socket. There are currently two types
  * DataType.IN – This is used to define incoming data into QuickServer.
  * DataType.OUT – This is used to define outgoing data from QuickServer.

One can change the default data-mode to any mode using QuickServer xml or by setting the mode by code.. below is an example..
```
      <default-data-mode>
               <data-type-in>Byte</data-type-in>
               <data-type-out>Byte</data-type-out>
       </default-data-mode>
```

or

```
quickserver.setDefaultDataMode(DataMode.STRING, DataType.IN);
```

The data mode of any client may be set by modifying ClientHandler object using the setDataMode() function. The format is given bellow
```
setDataMode(DataMode dataMode, DataType dataType)
```

# Changing DataMode #
One can change the DataMode any time during the life of the client using the following code.

Let's say you like to send out binary data and your default DataMode was String then you can use the following code.. to change to it..
```
    handler.setDataMode(DataMode.BINARY, DataType.OUT);
    handler.sendClientBinary(buffer,0, len);//now send out data
```

You may also change the incomming DataMode to say BYTE like this..
```
    handler.setDataMode(DataMode.BYTE, DataType.IN);
```

# Working with DataMode #

## `DataMode.STRING` ##

If DataType.IN is set `DataMode.STRING` then ClientCommandHandler implementation class is required by the QuickServer.

If DataType.OUT is set `DataMode.STRING` then you can use `sendClientMsg(String msg)` to send String data to client. `\r\n` will be appended by QuickServer to the msg to the end of the string.

## `DataMode.BYTE` ##
If DataType.IN is set `DataMode.BYTE` then ClientCommandHandler implementation class is required by the QuickServer.

If DataType.OUT is set `DataMode.BYTE` then you can use `sendClientBytes(java.lang.String msg)` to send String data to client.

## `DataMode.BINARY` ##
If DataType.IN is set `DataMode.BINARY` then ClientBinaryHandler implementation class is required by the QuickServer.

If DataType.OUT is set `DataMode.BINARY` then you can use `sendClientBinary(byte[] data)` to send String data to client.

## `DataMode.OBJECT` ##
If DataType.IN is set `DataMode.OBJECT` then ClientObjectHandler implementation class is required by the QuickServer.

If DataType.OUT is set `DataMode.OBJECT` then you can use `sendClientObject(java.lang.Object msg)` to send String data to client.

**Note:** When mode is DataMode.OBJECT and type is DataType.IN this call will block until the client ObjectOutputStream has written and flushes the header.

In next chapters lets try to explore the other modes of data exchange.