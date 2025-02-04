# Troubleshooting<a name="dt-afr-troublshooting"></a>

Each test execution has a unique test ID that is used to create the `uuid` directory\. Individual test group logs are under the `uuid` directory\. Use the AWS IoT console to look up the test group that failed and then open the log file for that group in the `/results/<uuid>` directory\. The information in this file includes: 
+ Full build and flash command output\.
+ Test execution output\.
+ More verbose IDT for Amazon FreeRTOS console output\.

We recommend the following workflow for troubleshooting:

1. Read the console output to find information, such as execution UUID and currently executing tasks\.

1. Look in the `AFQ_Report.xml` file for error statements from each test\. This directory contains execution logs of each test group\.

1. Look in the logs files under `/results/<uuid>/logs`\.

1. Investigate one of the following problem areas:
   + Device configuration, such as JSON configuration files in the `/configs/` folder\.
   + Device interface\. Check the logs to determine which interface is failing\.
   + Device tooling\. Make sure that the toolchains for building and flashing the device are installed and configured correctly\.
   + Make sure that you have a clean, cloned version of the Amazon FreeRTOS source code\. Amazon FreeRTOS releases are tagged according to Amazon FreeRTOS version\. To clone a specific version of the code, use git clone \-\-branch * <version\-number>* https://github\.com/aws/amazon\-freertos\.git\.

## Troubleshooting Device Configuration<a name="troubleshoot-device-config"></a>

When you use IDT for Amazon FreeRTOS, you must get the correct configuration files in place before you execute the binary\. If you are getting parsing and configuration errors, your first step should be to locate and use a configuration template appropriate for your environment\. These templates are located in the `<IDT_ROOT>/configs` directory\.

If you are still having issues, see the following debugging process\.

### Where Do I Look?<a name="where-to-look"></a>

Start by reading the console output to find information, such as the execution UUID, which is referenced as `uuid` in this documentation\.

Next, look in the `AFQ_Report.xml` file in the `/results/<uuid>` directory\. This file contains all of the test cases that were run and error snippets for each failure\. To get all of the execution logs, look under `/results/<uuid>/<test-case-id>.log` for each test group\.

### Debugging Parsing Errors<a name="parse-error"></a>

Occasionally, a typo in a JSON configuration can lead to parsing errors\. Most of the time, the issue is a result of omitting a bracket, comma, or quote from your JSON file\. IDT for Amazon FreeRTOS performs JSON validation and prints debugging information\. It prints the line where the error occurred, the line number, and the column number of the syntax error\. This information should be enough to help you fix the error, but if you are still having issues locating the error, you can perform validation manually in your IDE, a text editor such as Atom or Sublime, or through an online tool like JSONLint\.

### Debugging Required Parameter Missing Error<a name="param-missing"></a>

Because new features are being added to IDT for Amazon FreeRTOS, changes to the configuration files might be introduced\. Using an old configuration file might break your configuration\. If this happens, the `<test_case_id>.log` file under `/results/<uuid>/logs` explicitly lists all missing parameters\. IDT for Amazon FreeRTOS validates your JSON configuration file schemas to ensure that the latest supported version has been used\.

### Debugging Could Not Start Test Error<a name="could-not-start-test"></a>

You might encounter errors that point to failures during test start\. Because there are several possible causes, check the following areas for correctness:
+ Make sure that the pool name you've included in your execution command actually exists\. This is referenced directly from your `device.json` file\.
+ Make sure that the device or devices in your pool have correct configuration parameters\.

### Network Test Erros<a name="network-test-errors"></a>

For network\-based tests, IDT starts an echo server that binds to a non\-reserved port on the host machine\. If you are running into errors due to timeouts or unavailable connections, make sure that your network is configured to allow traffic to at least one port in the following ranges:
+ Unsecure ports: 1025\-33280
+ Secure ports: 33281\-65535

### Debugging Device Interface and Port Errors<a name="device-interface"></a>

This section contains information about the device interfaces IDT uses to connect to your devices\.

#### Supported Platforms<a name="platform-differences"></a>

IDT supports Linux, macOS, and Windows\. All three platforms have different naming schemes for serial devices that are attached to them:
+ Linux: `/dev/tty*`
+ macOS: `/dev/tty.*` or `/dev/cu.*`
+ Windows: COM\*

To check your device port:
+ For Linux/macOS, open a terminal and run ls /dev/tty\*\.
+ For macOS, open a terminal and run ls /dev/tty\.\* or ls /dev/cu\.\*\.
+ For Windows, open Device Manager and expand the serial devices group\.

To verify which device is connected to a port:
+ For Linux, make sure that the `udev` package is installed, and then run udevadm info –name=*<PORT>*\. This utility prints the device driver information that helps you verify you are using the correct port\. 
+ For macOS, open Launchpad and search for **System Information**\.
+ For Windows, open Device Manager and expand the serial devices group\.

#### Device Interfaces<a name="device-interfaces"></a>

Each embedded device is different, which means that they can have one or more serial ports\. It is common for devices to have two ports when connected to a machine:
+ A data port for flashing the device\.
+ A read port to read output\.

  You must set the correct read port in your `device.json` file\. Otherwise, reading output from the device might fail\.

  In the case of multiple ports, make sure to use the read port of the device in your `device.json` file\. For example, if you plug in an Espressif WRover device and the two ports assigned to it are `/dev/ttyUSB0` and `/dev/ttyUSB1`, use `/dev/ttyUSB1` in your `device.json` file\.

  For Windows, follow the same logic\.

#### Reading Device Data<a name="reading-device-data"></a>

IDT for Amazon FreeRTOS uses individual device build and flash tooling to specify port configuration\. If you are testing your device and don't get output, try the following default settings:
+ Baud rate: 115200
+ Data bits: 8
+ Parity: None
+ Stop bits: 1
+ Flow control: None

These settings are handled by IDT for Amazon FreeRTOS\. You do not have to set them\. However, you can use the same method to manually read device output\. On Linux or macOS, you can do this with the screen command\. On Windows, you can use a program such as TeraTerm\.

`Screen: screen /dev/cu.usbserial 115200`

`TeraTerm: Use the above-provided settings to set the fields explicitly in the GUI.`

### Development Toolchain Problems<a name="dev-toolchain"></a>

This section discusses problems that can occur with your toolchain\.

#### Code Composer Studio on Ubuntu<a name="ccs-ubuntu"></a>

Newer versions of Ubuntu \(17\.10 and 18\.04\) have a version of the `glibc` package that is not compatible with Code Composer Studio 7\.*x* versions\. We recommended that you install Code Composer Studio version 8\.2 or later\.

Symptoms of incompatibility might include:
+ Amazon FreeRTOS failing to build or flash to your device\.
+ The Code Composer Studio installer might freeze\.
+ No log output is displayed in the console during the build or flash process\.
+ Build command attempts to launch in GUI mode even when invoked as headless\.

### Logging<a name="dt-logging"></a>

IDT for Amazon FreeRTOS logs are placed in a single location\. From the root IDT directory, the available files are:
+ `./results/<uuid>`
+ `AFQ_Report.xml`
+ `awsiotdevicetester_report.xml`
+ `/logs/<test_group_id>.log`

The most important logs to look at are:
+ `results.xml`, which contains error messages about which tests failed\.
+ `<test_group_id>.log`, which you can use to dig further into the problem\.

### Debugging Amazon FreeRTOS<a name="debug-afr"></a>

When a source code error occurs, IDT for Amazon FreeRTOS writes debug output to the `<test-group-id>.log` file in the `/results/<uuid>/logs` directory\. Search the file for any instances of errors\. The error indicates a location in the Amazon FreeRTOS source code\. You can use the line number and file path information in that log to find the line of source code that caused the error\.

### Log Errors<a name="err-log"></a>

The `<test_group_id>.log` file is located in the `/results/<uuid>` directory\. Each test execution has a unique test ID that is used to create the *<uuid>* directory\. Individual test group logs are under the `<uuid>` directory\. Use the AWS IoT console to look up the test group that failed and then open the log file for that group in the `/results/<uuid>` directory\. The information in this file includes:
+ Full build and flash command output\.
+ Test execution output\.
+ More verbose IDT for Amazon FreeRTOS console output\.

### Console Errors<a name="err-console"></a>

When IDT for Amazon FreeRTOS is run, failures are reported to console with brief messages\. Look in `<test_group_id>.log` to learn more about the error\.