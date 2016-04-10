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
