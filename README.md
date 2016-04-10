# java-simple-serial-connector
Automatically exported from code.google.com/p/java-simple-serial-connector


jSSC - java serial port communication library

<wiki:gadget url="http://java-simple-serial-connector.googlecode.com/svn/trunk/social/social.xml" border="0" width="100%" height="128"/> jSSC (Java Simple Serial Connector) - library for working with serial ports from Java. jSSC support Win32(Win98-Win8), Win64, Linux(x86, x86-64, ARM), Solaris(x86, x86-64), Mac OS X 10.5 and higher(x86, x86-64, PPC, PPC64). Documentation and examples you can find in wiki.

jSSC-2.8.0 published in Maven Central Repo: http://search.maven.org/#search|ga|1|jssc

https://java-simple-serial-connector.googlecode.com/svn/trunk/additional_content/icons/Maven_logo.png 

jSSC-2.8.0 Changelog

Fixes:
Important! Fixed bug with port handles potential leakage. Thanks to Cristian Maglie for complete bug-report
Additions:
Added method "writeString(String string, String charsetName)"
Added method "getNativeLibraryVersion" in "SerialNativeInterface" class
Enabled Java and Native libraries versions mismatch check
All new downloads will be available on GitHub: https://github.com/scream3r/java-simple-serial-connector/releases'>https://github.com/scream3r/java-simple-serial-connector/releases

jSSC-2.6.0 Changelog

Note:
Linux x86 and x86-64 was builded on Ubuntu 10.04 and don't depends GLIBC-2.15 unlike jSSC-2.5.0
Additions:
Added os.name - "Darwin" and os.arch - "universal" support. It can be useful for MacOS X developers
Added ttyO to Linux RegExp for listing OMAP serial devices
Added JSSC_IGNPAR and JSSC_PARMRK properties for enabling IGNPAR and PARMRK flags in _nix termios structure
jSSC-2.5.0 Changelog

Fixes:
Important! Fixed bug in MacOS X native readBytes() method
Important! Fixed bug with garbage reading on Linux, MacOSX, Solaris, cause of incorrect using of VMIN and VTIME. Now "read" methods works correctly and are blocking like in Windows
Important! Fixed error with garbage reading in Windows using jSSC after another application used serial port. To prevent this effect COMMTIMEOUTS structure zeroing added to setParams() method
Important! The port handle now stored in variable of type "long" instead of "int", to prevent potential problems with type conversions on Win64
Fixed MacOS X 10.8 bug with native lib loading (.dylib -> .jnilib)
Fixed Linux error with exclusive access to serial port (TIOCEXCL). TIOCNXCL added to closePort() method for clearing exclusive access
Fixed Windows native lib port name concatenation error
Fixed native lib extraction path if user home is read only, in this situation lib will be extracted to tmp folder
Null port name fix. If try to invoke method openPort() for SerialPort(null) object, exception TYPE_NULL_NOT_PERMITTED will be thrown
Enabled TIOCEXCL support in Solaris
Additions:
Added ARM Soft & Hard float support (Tested of Raspberry Pi with Oracle JDK(6-7-8))
Added ttyACM, ttyAMA, rfcomm to Linux RegExp and tty.usbmodem to MacOS X RegExp
Added precompiled RegExp's for Linux, Solaris, MacOS X for more faster port listing
Added private common for Linux, Solaris, MacOS X method getUnixBasedPortNames() for listing serial ports
Rewrited comparator for sorting port names. Now it's a common comparator for Windows, Linux, Solaris and MacOS X
Added some syntax sugar to SerialPortList class, for changing search path, RegExp and comparator
Added timeouts for read operations and SerialPortTimeoutException class for catching timeout exceptions
Added JSSC_NO_TIOCEXCL JVM property for disable using of exclusive access to serial port
Added termios(_nix) and DCB(Windows) structure cheking on port opening, it helps separate real serial devices from others
Added "ERR_" constants into SerialNativeInterface
Added new exception TYPE_INCORRECT_SERIAL_PORT
Added new exception TYPE_PERMISSION_DENIED. It can be very useful for _nix based system if user have no permissions for using serial device

jSSC-0.7 examples

Getting serial ports names

```

import jssc.SerialPortList;

public class Main {

public static void main(String[] args) {
    String[] portNames = SerialPortList.getPortNames();
    for(int i = 0; i < portNames.length; i++){
        System.out.println(portNames[i]);
    }
}
}

```

Writing data to serial port

```

import jssc.SerialPort; import jssc.SerialPortException;

public class Main {

public static void main(String[] args) {
    SerialPort serialPort = new SerialPort("COM1");
    try {
        serialPort.openPort();//Open serial port
        serialPort.setParams(SerialPort.BAUDRATE_9600, 
                             SerialPort.DATABITS_8,
                             SerialPort.STOPBITS_1,
                             SerialPort.PARITY_NONE);//Set params. Also you can set params by this string: serialPort.setParams(9600, 8, 1, 0);
        serialPort.writeBytes("This is a test string".getBytes());//Write data to port
        serialPort.closePort();//Close serial port
    }
    catch (SerialPortException ex) {
        System.out.println(ex);
    }
}
}

```

Reading data from serial port

```

import jssc.SerialPort; import jssc.SerialPortException;

public class Main {

public static void main(String[] args) {
    SerialPort serialPort = new SerialPort("COM1");
    try {
        serialPort.openPort();//Open serial port
        serialPort.setParams(9600, 8, 1, 0);//Set params.
        byte[] buffer = serialPort.readBytes(10);//Read 10 bytes from serial port
        serialPort.closePort();//Close serial port
    }
    catch (SerialPortException ex) {
        System.out.println(ex);
    }
}
}

```

Event mask & SerialPortEventListener interface

Note: The mask is an additive quantity, thus to set a mask on the expectation of the arrival of Event Data (MASK_RXCHAR) and change the status lines CTS (MASK_CTS), DSR (MASK_DSR) we just need to combine all three masks.
```

import jssc.SerialPort; import jssc.SerialPortEvent; import jssc.SerialPortEventListener; import jssc.SerialPortException;

public class Main {

static SerialPort serialPort;

public static void main(String[] args) {
    serialPort = new SerialPort("COM1"); 
    try {
        serialPort.openPort();//Open port
        serialPort.setParams(9600, 8, 1, 0);//Set params
        int mask = SerialPort.MASK_RXCHAR + SerialPort.MASK_CTS + SerialPort.MASK_DSR;//Prepare mask
        serialPort.setEventsMask(mask);//Set mask
        serialPort.addEventListener(new SerialPortReader());//Add SerialPortEventListener
    }
    catch (SerialPortException ex) {
        System.out.println(ex);
    }
}

/*
 * In this class must implement the method serialEvent, through it we learn about 
 * events that happened to our port. But we will not report on all events but only 
 * those that we put in the mask. In this case the arrival of the data and change the 
 * status lines CTS and DSR
 */
static class SerialPortReader implements SerialPortEventListener {

    public void serialEvent(SerialPortEvent event) {
        if(event.isRXCHAR()){//If data is available
            if(event.getEventValue() == 10){//Check bytes count in the input buffer
                //Read data, if 10 bytes available 
                try {
                    byte buffer[] = serialPort.readBytes(10);
                }
                catch (SerialPortException ex) {
                    System.out.println(ex);
                }
            }
        }
        else if(event.isCTS()){//If CTS line has changed state
            if(event.getEventValue() == 1){//If line is ON
                System.out.println("CTS - ON");
            }
            else {
                System.out.println("CTS - OFF");
            }
        }
        else if(event.isDSR()){///If DSR line has changed state
            if(event.getEventValue() == 1){//If line is ON
                System.out.println("DSR - ON");
            }
            else {
                System.out.println("DSR - OFF");
            }
        }
    }
}
}

```
