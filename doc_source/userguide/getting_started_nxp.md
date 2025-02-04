# Getting Started with the NXP LPC54018 IoT Module<a name="getting_started_nxp"></a>

This tutorial provides instructions for getting started with the NXP LPC54018 IoT Module\. If you do not have an NXP LPC54018 IoT Module, visit the AWS Partner Device Catalog to purchase one from our [partner](https://devices.amazonaws.com/detail/a3G0L00000AANtAUAX/LPC54018-IoT-Solution)\. Use a USB cable to connect your NXP LPC54018 IoT Module to your computer\.

Before you begin, you must configure AWS IoT and your Amazon FreeRTOS download to connect your device to the AWS Cloud\. See [First Steps](freertos-prereqs.md) for instructions\. In this tutorial, the path to the Amazon FreeRTOS download directory is referred to as `BASE_FOLDER`\.

## Overview<a name="w3aab7c19c27b7"></a>

This tutorial contains instructions for the following getting started steps:

1. Installing software on the host machine for developing and debugging embedded applications for your microcontroller board\.

1. Cross compiling an Amazon FreeRTOS demo application to a binary image\.

1. Loading the application binary image to your board, and then running the application\.

## Set Up Your Development Environment<a name="setup-env_nxp"></a>

Amazon FreeRTOS supports two IDEs for the NXP LPC54018 IoT Module: IAR Embedded Workbench and MCUXpresso\.

Before you begin, install one of these IDEs\.

**To install IAR Embedded Workbench for ARM**

1. Browse to [Software for NXP Kits](https://www.iar.com/iar-embedded-workbench/partners/nxp/downloads-for-nxp-kits) and choose **Download Software**\.
**Note**  
IAR Embedded Workbench for ARM requires Microsoft Windows\.

1. Unzip and run the installer\. Follow the prompts\.

1. In the **License Wizard**, choose **Register with IAR Systems to get an evaluation license**\.

**To install MCUXpresso from NXP**

1. Download and run the MCUXpresso installer from [NXP](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)\.

1. Browse to [MCUXpresso SDK](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-software-development-kit-sdk:MCUXpresso-SDK) and choose **Build your SDK\.**

1. Choose **Select Development Board**\.

1. Under **Select Development Board**, in **Search by Name**, enter **LPC54018\-IoT\-Module**\.

1. Under **Boards**, choose **LPC54018\-IoT\-Module**\.

1. Verify the hardware details, and then choose **Build MCUXepresso SDK**\.

1. The SDK for Windows using the MCUXpresso IDE is already built\. Choose **Download SDK**\. If you are using another operating system, under **Host OS**, choose your operating system, and then choose **Download SDK**\. 

1. Start the MCUXpresso IDE, and choose the **Installed SDKs** tab\.

1. Drag and drop the downloaded SDK archive file into the **Installed SDKs** window\.

If you experience issues during installation, see [NXP Support](https://www.nxp.com/support/support:SUPPORTHOME?tid=sbmenu) or [NXP Developer Resources](https://www.nxp.com/support/developer-resources:DEVELOPER_HOME)\.

### Connecting a JTAG Debugger<a name="get-started-jtag-debugger"></a>

You need a JTAG debugger to launch and debug your code running on the NXP LPC54018 board\. Amazon FreeRTOS was tested using a Segger J\-Link probe\. For more information about supported debuggers, see the [NXP LPC54018 User Guide](https://www.nxp.com/docs/en/user-guide/UM11078.pdf)\.

**Note**  
If you are using a Segger J\-Link debugger, you need a converter cable to connect the 20\-pin connector from the debugger to the 10\-pin connector on the NXP IoT module\. 

## Build and Run the Amazon FreeRTOS Demo Project<a name="nxp-build-and-run"></a>

### Import the Amazon FreeRTOS Demo into Your IDE<a name="nxp-freertos-import-project"></a><a name="nxp-load-project"></a>

**To import the Amazon FreeRTOS sample code into the IAR Embedded Workbench IDE**

1. Open IAR Embedded Workbench, and from the **File** menu, choose **Open Workspace**\.

1. In the **search\-directory** text box, enter `<BASE_FOLDER>\demos\nxp\lpc54018_iot_module\iar`, and choose **aws\_demos\.eww**\.

1. From the **Project** menu, choose **Rebuild All**\.

**To import the Amazon FreeRTOS sample code into the MCUXpresso IDE**

1. Open MCUXpresso, and from the **File** menu, choose **Open Projects From File System**\.

1. In the **Directory** text box, enter `<BASE_FOLDER>\demos\nxp\lpc54018_iot_module\mcuxpresso`, and choose **Finish**

1. From the **Project** menu, choose **Build All**\.

### Run the Amazon FreeRTOS Demo Project<a name="nxp-run-example"></a>

1. Connect the USB port on the NXP IoT Module to your host computer, open a terminal program, and connect to the port identified as USB Serial Device\.

1. In your IDE, from the **Project** menu, choose **Build**\.

1. Connect the NXP IoT Module and the Segger J\-Link Debugger to the USB ports on your computer using mini\-USB to USB cables\.

1. If you are using IAR Embedded Workbench:

   1. From the **Project** menu, choose **Download and Debug**\.

   1. From the **Debug** menu, choose **Start Debugging**\.

   1. When the debugger stops at the breakpoint in `main`, from the **Debug** menu, choose **Go\.**
**Note**  
If a **J\-Link Device Selection** dialog box opens, choose **OK** to continue\. In the **Target Device Settings** dialog box, choose **Unspecified**, choose **Cortex\-M4**, and then choose **OK**\. You only need to be do this once\.

1. If you are using MCUXpresso:

   1. If this is your first time debugging, choose the `aws_demos` project and from the **Debug** toolbar, choose the blue debug button\.

   1. Any detected debug probes are displayed\. Choose the probe you want to use, and then choose **OK** to start debugging\.
**Note**  
When the debugger stops at the breakpoint in `main()`, press the debug restart button ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/freertos/latest/userguide/images/reset.png) once to reset the debugging session\. \(This is required due to a bug with MCUXpresso debugger for NXP54018\-IoT\-Module\)\.

1. When the debugger stops at the breakpoint in `main()`, from the **Debug** menu, choose **Go**\.

You can use the MQTT client in the AWS IoT console to monitor the messages that your device sends to the AWS Cloud\.

**To subscribe to the MQTT topic with the AWS IoT MQTT client**

1. Sign in to the [AWS IoT console](https://console.aws.amazon.com/iotv2/)\.

1. In the navigation pane, choose **Test** to open the MQTT client\.

1. In **Subscription topic**, enter **freertos/demos/echo**, and then choose **Subscribe to topic**\.

## Troubleshooting<a name="getting_started_nxp_troubleshooting"></a>

For general troubleshooting information, see [Troubleshooting Getting Started](gsg-troubleshooting.md)\.