# Getting Started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT<a name="getting_started_espressif"></a>

This tutorial provides instructions for getting started with the Espressif ESP32\-DevKitC and the ESP\-WROVER\-KIT\. If you don't have an Espressif ESP32\-DevKitC, you can purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtjUAH/ESP32-DevKitC) on the AWS Partner Device Catalog\. If you don't have an Espressif ESP32\-WROVER\-KIT, you can purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtlUAH/ESP-WROVER-KIT) on the AWS Partner Device Catalog\. Both devices are supported on Amazon FreeRTOS\. For more information about these boards, see [ESP32\-DevKitC](https://docs.espressif.com/projects/esp-idf/en/latest/hw-reference/modules-and-boards.html#esp32-devkitc-v4) or [ESP\-WROVER\-KIT](https://docs.espressif.com/projects/esp-idf/en/latest/hw-reference/modules-and-boards.html#esp-wrover-kit-v4-1) on the Espressif website\.

**Note**  
Currently, the Amazon FreeRTOS port for ESP32\-WROVER\-KIT and ESP DevKitC does not support the following features:  
Lightweight IP\.
Symmetric multiprocessing \(SMP\)\.

## Overview<a name="w3aab7c19c13b7"></a>

This tutorial contains instructions for the following getting started steps:
+ Connecting your board to a host machine\.
+ Installing software on the host machine that you use to develop and debug embedded applications for your microcontroller board\.
+ Cross\-compiling an Amazon FreeRTOS demo application to a binary image\.
+ Loading the application binary image to your board, and then running the application\.
+ Interacting with the application running on your board across a serial connection, for monitoring and debugging purposes\.

## Prerequisites<a name="setup-espressif-prereqs"></a>

Before you get started with Amazon FreeRTOS on your Espressif board, you need to set up your AWS account and permissions\.

To create an AWS account, see [Create and Activate an AWS Account](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)\.

To add an IAM user to your AWS account, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. To grant your IAM user account access to AWS IoT and Amazon FreeRTOS, attach the following IAM policies to your IAM user account:
+ `AmazonFreeRTOSFullAccess`
+ `AWSIoTFullAccess`

**To attach the AmazonFreeRTOSFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AmazonFreeRTOSFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

**To attach the AWSIoTFullAccess policy to your IAM user**

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home), and from the navigation pane, choose ** Users**\.

1. Enter your user name in the search text box, and then choose it from the list\.

1. Choose **Add permissions**\.

1. Choose **Attach existing policies directly**\.

1. In the search box, enter **AWSIoTFullAccess**, choose it from the list, and then choose **Next: Review**\.

1. Choose **Add permissions**\.

For more information about IAM and user accounts, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

For more information about policies, see [IAM Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_access-management.html)\.

## Set Up the Espressif Hardware<a name="setup-hw-espressif"></a>

See the [ESP32\-DevKitC Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-devkitc.html) for information about setting up the ESP32\-DevKitC development board hardware\.

See the [ESP\-WROVER\-KIT Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/get-started-wrover-kit.html) for information about setting up the ESP\-WROVER\-KIT development board hardware\.

**Note**  
Do not proceed to the **Get Started** section of the Espressif guides\. Instead, follow the steps below\.

## Set Up Your Development Environment<a name="setup-env-esspressif"></a>

To communicate with your board, you need to download and install a toolchain\.

### Setting Up the Toolchain<a name="setup-toolchain"></a>

To set up the toolchain, follow the instructions for your host machine's operating system:

**Note**  
When you reach the "Get ESP\-IDF" instructions under **Next Steps**, stop and return to the instructions on this page\.   If you previously followed the "Get ESP\-IDF" instructions and installed ESP\-IDF, make sure that you clear the  `IDF_PATH` environment variable from your system before continuing\.
+ [Standard Setup of Toolchain for Windows]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/windows-setup.html)
+ [Standard Setup of Toolchain for macOS]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/macos-setup.html)
+ [Standard Setup of Toolchain for Linux]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/linux-setup.html)

## Establish a Serial Connection<a name="establish-serial-connection"></a>

To establish a serial connection between your host machine and the ESP32\-DevKitC, you must install CP210x USB to UART Bridge VCP drivers\. You can download these drivers from [Silicon Labs](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers)\. 

To establish a serial connection between your host machine and the ESP32\-WROVER\-KIT, you must install some FTDI virtual COM port drivers\. You can download these drivers from [FTDI](https://www.ftdichip.com/Drivers/VCP.htm)\.

For more information, see [Establish Serial Connection with ESP32](https://docs.espressif.com/projects/esp-idf/en/latest/get-started/establish-serial-connection.html)\. After you establish a serial connection, make a note of the serial port for your board's connection\. You need it when you build the demo\.

## Download and Configure Amazon FreeRTOS<a name="download-and-configure-espressif"></a>

After your environment is set up, you can download Amazon FreeRTOS from GitHub\. Configurations of Amazon FreeRTOS for the Espressif boards are not available from the Amazon FreeRTOS console\.

### Downloading Amazon FreeRTOS<a name="download-espressif"></a>

Clone or download the `amazon-freertos` repository from [GitHub](https://github.com/aws/amazon-freertos)\.

**Note**  
The maximum length of a file path on Microsoft Windows is 260 characters\. Lengthy Amazon FreeRTOS download directory paths can cause build failures\.  
In this tutorial, the path to the `amazon-freertos` directory is referred to as *<amazon\-freertos>*\.

### Configure the Amazon FreeRTOS Demo Applications<a name="config-demos"></a>

1. If you are running macOS or Linux, open a terminal prompt\. If you are running Windows, open mingw32\.exe\.

1. To verify that you have Python 2\.7\.10 or later installed, run python \-\-version\. The version installed is displayed\. If you do not have Python 2\.7\.10 or later installed, you can install it from the [Python website](https://www.python.org/downloads/)\.

1. You need the AWS CLI to run AWS IoT commands\. If you are running Windows, use the easy\_install awscli to install the AWS CLI in the mingw32 environment\.

   If you are running macOS or Linux, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html)\. 

1. Run aws configure and configure the AWS CLI with your AWS access key ID, secret access key, and default region name\. For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

1. Use the following command to install the AWS SDK for Python \(boto3\):
   + On Windows, in the mingw32 environment, run easy\_install boto3\.
   + On macOS or Linux, run pip install tornado nose \-\-user and then run pip install boto3 \-\-user\.

Amazon FreeRTOS includes the `SetupAWS.py` script to make it easier to set up your Espressif board to connect to AWS IoT\. To configure the the script, open `<BASE_FOLDER>/tools/aws_config_quick_start/configure.json` and set the following attributes:

`afr_source_dir`  
The complete path to the `amazon-freertos` directory on your computer\. Make sure that you use forward slashes to specify this path\.

`thing_name`  
The name that you want to assign to the AWS IoT thing that represents your board\.

`wifi_ssid`  
The SSID of your Wi\-Fi network\.

`wifi_password`  
The password for your Wi\-Fi network\.

`wifi_security`  
The security type for your Wi\-Fi network\.   
Valid security types are:  
+ `eWiFiSecurityOpen` \(Open, no security\)
+ `eWiFiSecurityWEP` \(WEP security\)
+ `eWiFiSecurityWPA` \(WPA security\)
+ `eWiFiSecurityWPA2` \(WPA2 security\)

**To run the configuration script**

1. If you are running macOS or Linux, open a terminal prompt\. If you are running Windows, open mingw32\.exe\.

1. Go to the `<BASE_FOLDER>/tools/aws_config_quick_start` directory and run python SetupAWS\.py setup\.

The script does the following:
+ Creates an IoT thing, certificate, and policy
+ Attaches the IoT policy to the certificate and the certificate to the AWS IoT thing
+ Populates the `aws_clientcredential.h` file with your AWS IoT endpoint, Wi\-Fi SSID, and credentials
+ Formats your certificate and private key and writes them to the `aws_clientcredential.h` header file

For more information about `SetupAWS.py`, see the README\.md in the `<BASE_FOLDER>/tools/aws_config_quick_start` directory\.

## Build and Run the Amazon FreeRTOS Demo Project<a name="build-and-run-example-espressif"></a>

**To configure your board's connection for flashing the demo**

1. Connect your board to your host computer\.

1. If you are using macOS or Linux, open a terminal\. If you are using Windows, open mingw32\.exe \(downloaded from mysys toolchain\)\.

1. Navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make` and enter following command to make and open the Espressif IoT Development Framework Configuration menu:

   ```
   make menuconfig
   ```

   To confirm a selection in the menu, choose **Select**\. To save a configuration, choose **Save**\. To exit the menu, choose **Exit**\.

1. In the Espressif IoT Development Framework Configuration menu, navigate to **Serial flasher config**\.

   Choose **Default serial port** to configure the serial port\. The serial port you configure here is used to write the demo application to your board\. On Windows, serial ports have names like `COM1`\. On macOS, they start with `/dev/cu`\. On Linux, they start with `/dev/tty`\. For more information, see [Finding Your Board's Serial Port](uart-term.md#serial-port-ts)\.

   Choose **Default baud rate** to change the default baud rate to use while communicating with your board\. Increasing the baud rate can reduce the time required to flash your board\. Depending on your hardware, you can increase the default baud rate up to 921600\.

1. After you set up your board's configuration, save and exit the menu\.

To build and flash firmware \(including boot loader and partition table\), and to monitor serial console output, open a terminal \(macOS and Linux\) or mingw32\.exe \(Windows\)\. Change directories to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make` and run the following command:

```
make flash monitor
```

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

`Hello World number` and `Hello World number ACK` messages appear at the bottom of the MQTT client page when the terminal or command prompt where you issued the `make flash monitor` command returns output\.

```
12 1350 [MQTTEcho] Echo successfully published 'Hello World 0'
13 1357 [Echoing] Message returned with ACK: 'Hello World 0 ACK'
14 1852 [MQTTEcho] Echo successfully published 'Hello World 1'
15 1861 [Echoing] Message returned with ACK: 'Hello World 1 ACK'
16 2355 [MQTTEcho] Echo successfully published 'Hello World 2'
17 2361 [Echoing] Message returned with ACK: 'Hello World 2 ACK'
18 2857 [MQTTEcho] Echo successfully published 'Hello World 3'
19 2863 [Echoing] Message returned with ACK: 'Hello World 3 ACK'
```

When the demo finishes running, you should see output similar to the following in your terminal or command prompt:

```
32 6380 [MQTTEcho] Echo successfully published 'Hello World 10'
33 6386 [Echoing] Message returned with ACK: 'Hello World 10 ACK'
34 6882 [MQTTEcho] Echo successfully published 'Hello World 11'
35 6889 [Echoing] Message returned with ACK: 'Hello World 11 ACK'
36 7385 [MQTTEcho] MQTT echo demo finished.
37 7385 [MQTTEcho] ----Demo finished----
```

### Run the Bluetooth Low\-Energy Demos<a name="espressif-run-ble"></a>


|  | 
| --- |
| Amazon FreeRTOS support for Bluetooth Low Energy is in public beta release\. BLE demos are subject to change\. | 

Amazon FreeRTOS supports [Bluetooth Low Energy \(BLE\)](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-library.html) connectivity\. You can download Amazon FreeRTOS with BLE from [GitHub](https://github.com/aws/amazon-freertos/tree/feature/ble-beta)\. The Amazon FreeRTOS BLE library is still in public beta, so you need to switch branches to access the code for your board\. Check out the branch named `feature/ble-beta`\.

To run the Amazon FreeRTOS demo project across BLE, you need to run the Amazon FreeRTOS BLE Mobile SDK Demo Application on an iOS or Android mobile device\.

**To set up the the Amazon FreeRTOS BLE Mobile SDK demo application**

1. Follow the instructions in [Mobile SDKs for Amazon FreeRTOS Bluetooth Devices](https://docs.aws.amazon.com/freertos/latest/userguide/freertos-ble-mobile.html) to download and install the SDK for your mobile platform on your host computer\.

1. Follow the instructions in [Amazon FreeRTOS BLE Mobile SDK Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-sdk-app) to set up the demo mobile application on your mobile device\.

For instructions about how to run the MQTT over BLE demo on your board, see the [MQTT over BLE Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-mqtt)\.

For instructions about how to run the Wi\-Fi provisioning demo on your board, see the [Wi\-Fi Provisioning Demo Application](https://docs.aws.amazon.com/freertos/latest/userguide/ble-demo.html#ble-demo-wifi)\.

## Troubleshooting<a name="getting_started_espressif_troubleshooting"></a>
+ If you are running macOS and the operating system does not recognize your ESP\-WROVER\-KIT, make sure you do not have the D2XX drivers installed\. To uninstall them, follow the instructions in the [FTDI Drivers Installation Guide for macOS X](http://www.ftdichip.com/Support/Documents/AppNotes/AN_134_FTDI_Drivers_Installation_Guide_for_MAC_OSX.pdf)\.
+ The monitor utility provided by ESP\-IDF \(and invoked using make monitor\) helps you decode addresses\. For this reason, it can help you get some meaningful backtraces in the event the application crashes\. For more information, see [Automatically Decoding Addresses]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/idf-monitor.html#automatically-decoding-addresses) on the Espressif website\.
+ It is also possible to enable GDBstub for communication with gdb without requiring any special JTAG hardware\. For more information, see [Launch GDB for GDBStub]( https://docs.espressif.com/projects/esp-idf/en/latest/get-started/idf-monitor.html#launch-gdb-for-gdbstub) on the Espressif website\.
+ For information about setting up an OpenOCD\-based environment if JTAG hardware\-based debugging is required, see [JTAG Debugging]( https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/jtag-debugging) on the Espressif website\.
+ If `pyserial` cannot be installed using `pip` on macOS, download it from the [pyserial website](https://pypi.org/simple/pyserial)\.
+ If the board resets continuously, try erasing the flash by entering the following command on the terminal:

  ```
  make erase_flash
  ```
+ If you see errors when you run `idf_monitor.py`, use Python 2\.7\.
+ Required libraries from ESP\-IDF are included in Amazon FreeRTOS, so there is no need to download them externally\. If the `IDF_PATH` environment variable is set, we recommend that you clear it before you build Amazon FreeRTOS\. 
+ On Windows, it can take 3\-4 minutes for the project to build\. You can use the `-j4` switch on the make command to reduce the build time:

  ```
  make flash monitor -j4
  ```
+ If your device has trouble connecting to AWS IoT, open the `aws_clientcredential.h` file, and verify that the configuration variables are properly defined in the file\. `clientcredentialMQTT_BROKER_ENDPOINT[]` should look like `<1234567890123>-ats.iot.<us-east-1>.amazonaws.com`\.

For general troubleshooting information, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.

### Debugging Code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT<a name="debugging-espressif"></a>

You need a JTAG to USB cable\. We use a USB to MPSSE cable \(for example, the [FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Products/Cables/USBMPSSE.htm)\)\. 

#### ESP\-DevKitC JTAG Setup<a name="jtag-devkitc"></a>

For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32 DevkitC:


| C232HM\-DDHSL\-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name | 
| --- | --- | --- | 
|  Brown \(pin 5\)  |  IO14  | TMS | 
|  Yellow \(pin 3\)  |  IO12  | TDI | 
| Black \(pin 10\) | GND | GND | 
| Orange \(pin 2\) | IO13 | TCK | 
| Green \(pin 4\) | IO15 | TDO | 

#### ESP\-WROVER\-KIT JTAG Setup<a name="jtag-wrover"></a>

For the FTDI C232HM\-DDHSL\-0 cable, these are the connections to the ESP32\-WROVER\-KIT:


| C232HM\-DDHSL\-0 Wire Color | ESP32 GPIO Pin | JTAG Signal Name | 
| --- | --- | --- | 
|  Brown \(pin 5\)  |  IO14  |  TMS  | 
|  Yellow \(pin 3\)  |  IO12  |  TDI  | 
|  Orange \(pin 2\)  |  IO13  |  TCK  | 
|  Green \(pin 4\)  |  IO15  |  TDO  | 

These tables were developed from the [ FTDI C232HM\-DDHSL\-0 datasheet](http://www.ftdichip.com/Support/Documents/DataSheets/Cables/DS_C232HM_MPSSE_CABLE.PDF)\. For more information, see C232HM MPSSE Cable Connection and Mechanical Details in the datasheet\.

To enable JTAG on the ESP\-WROVER\-KIT, place jumpers on the TMS, TDO, TDI, TCK, and S\_TDI pins as shown here:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/JP8-jumpers.png)

#### Debugging on Windows<a name="debugging-espressif-windows"></a>

**To set up for debugging on Windows**

1. Connect the USB side of the FTDI C232HM\-DDHSL\-0 to your computer and the other side as described in [Debugging Code on Espressif ESP32\-DevKitC and ESP\-WROVER\-KIT](#debugging-espressif)\. The FTDI C232HM\-DDHSL\-0 device should appear in **Device Manager** under **Universal Serial Bus Controllers**\.

1. Under the list of universal serial bus devices, right\-click the **C232HM\-DDHSL\-0** device, and choose **Properties**\.
**Note**  
The device might be listed as **USB Serial Port**\.

   In the properties window, choose the **Details** tab to see the properties of the device\. If the device is not listed, install the [Windows driver for FTDI C232HM\-DDHSL\-0](http://www.ftdichip.com/Drivers/D2XX.htm)\.

1. On the **Details** tab, choose **Property**, and then choose **Hardware IDs**\. You should see something like this in the **Value** field:

   ```
   FTDIBUS\COMPORT&VID_0403&PID_6014
   ```

   In this example, the vendor ID is 0403 and the product ID is 6014\.

   Verify these IDs match the IDs in `demos\espressif\esp32_devkitc_esp_wrover_kit\make\esp32_devkitj_v1.cfg`\. The IDs are specified in a line that begins with `ftdi_vid_pid` followed by a vendor ID and a product ID:

   ```
   ftdi_vid_pid 0x0403 0x6014
   ```

1. Download [OpenOCD for Windows](https://github.com/espressif/openocd-esp32/releases)\.

1. Unzip the file to `C:\` and add `C:\openocd-esp32\bin` to your system path\.

1. OpenOCD requires libusb, which is not installed by default on Windows\.

   To install libusb

   1. Download [zadig\.exe](https://zadig.akeo.ie)\.

   1. Run `zadig.exe`\. From the **Options** menu, choose **List All Devices**\.

   1. From the drop\-down menu, choose **C232HM\-DDHSL\-0**\.

   1. In the target driver field, to the right of the green arrow, choose **WinUSB**\.

   1. From the drop\-down box under the target driver field, choose the arrow, and then choose **Install Driver**\. Choose **Replace Driver**\.

1. Open a command prompt, navigate to `<BASE_FOLDER>\demos\espressif\esp32_devkitc_esp_wrover_kit\make` and run:

   ```
   openocd.exe -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

   Leave this command prompt open\.

1. Open a new command prompt, navigate to your `msys32` directory, and run `mingw32.exe`\. In the mingw32 terminal, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make` and run make flash monitor\.

1. Open another mingw32 terminal, navigate to `<BASE_FOLDER>\demos\espressif\esp32_devkitc_esp_wrover_kit\make` and wait until the demo starts running on your board\. When it does, run `xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf`\. The program should stop in the `main` function\.

**Note**  
The ESP32 supports a maximum of two break points\.

#### Debugging on macOS<a name="debugging-espressif-macos"></a>

1. Download the [FTDI driver for macOS](http://www.ftdichip.com/Drivers/VCP.htm)\.

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\.

1. Extract the downloaded \.tar file and set the path in `.bash_profile` to `<OCD_INSTALL_DIR>/openocd-esp32/bin`\.

1. Use the following command to install `libusb` on macOS:

   ```
   brew install libusb
   ```

1. Use the following command to unload the serial port driver:

   ```
   sudo kextunload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. If you are running a macOS version later than 10\.9, use the following command to unload the Apple FTDI driver:

   ```
   sudo kextunload -b com.apple.driver.AppleUSBFTDI
   ```

1. Use the following command to get the product ID and vendor ID of the FTDI cable\. It lists the attached USB devices:

   ```
   system_profiler SPUSBDataType
   ```

   The output from `system_profiler` should look like this:

   ```
   DEVICE:
   							
   Product ID: product-ID
   Vendor ID: vendor-ID (Future Technology Devices International Limited)
   ```

1. Open `demos/espressif/esp32_devkitc_esp_wrover_kit/make/esp32_devkitj_v1.cfg`\. The vendor ID and product ID for your device are specified in a line that begins with `ftdi_vid_pid`\. Change the IDs to match the IDs from the `system_profiler` output in the previous step\.

1. Open a terminal window, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and use the following command to run OpenOCD:

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

1. Open a new terminal, and use the following command to load the FTDI serial port driver:

   ```
   sudo kextload -b com.FTDI.driver.FTDIUSBSerialDriver
   ```

1. Navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   make flash monitor
   ```

1. Open another new terminal, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf
   ```

   The program should stop at `main()`\.

#### Debugging on Linux<a name="debugging-espressif-linux"></a>

1. Download [OpenOCD](https://github.com/espressif/openocd-esp32/releases)\. Extract the tarball and follow the installation instructions in the readme file\.

1. Use the following command to install libusb on Linux:

   ```
   sudo apt-get install libusb-1.0
   ```

1. Open a terminal and enter **ls \-l /dev/ttyUSB\*** to list all USB devices connected to your computer\. This helps you check if the board’s USB ports are recognized by the operating system\. You should see output like this:

   ```
   $ls -l /dev/ttyUSB*
   crw-rw----	1	root	dialout	188,	0	Jul	10	19:04	/dev/ttyUSB0
   crw-rw----	1	root	dialout	188,	1	Jul	10	19:04	/dev/ttyUSB1
   ```

1. Sign off and then sign in and cycle the power to the board to make the changes take effect\. In a terminal prompt, list the USB devices\. Make sure the group owner has changed from `dialout` to `plugdev`:

   ```
   $ls -l /dev/ttyUSB*
   crw-rw----	1	root	plugdev	188,	0	Jul	10	19:04	/dev/ttyUSB0
   crw-rw----	1	root	plugdev	188,	1	Jul	10	19:04	/dev/ttyUSB1
   ```

   The `/dev/ttyUSBn` interface with the lower number is used for JTAG communication\. The other interface is routed to the ESP32’s serial port \(UART\) and is used for uploading code to the ESP32’s flash memory\.

1. In a terminal window, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and use the following command to run OpenOCD:

   ```
   openocd -f esp32_devkitj_v1.cfg -f esp-wroom-32.cfg
   ```

1. Open another terminal, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   make flash monitor
   ```

1. Open another terminal, navigate to `<BASE_FOLDER>/demos/espressif/esp32_devkitc_esp_wrover_kit/make`, and run the following command:

   ```
   xtensa-esp32-elf-gdb -x gdbinit build/aws_demos.elf
   ```

   The program should stop in `main()`\.