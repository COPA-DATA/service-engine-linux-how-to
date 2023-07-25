# zenon Service Engine on Linux - Getting Started

This guide helps you with the initial installation and configuration of the zenon Service Engine on Linux.

## Requirements

**System Requirements**
- A Linux device satisfying one of the following hardware and OS combinations
    | Supported Architecture | Supported Operating System | Tested on HW |
    | - | - | - |
    | amd64 | Ubuntu 22.04 |  64 Bit VM, regular PC or industrial PC |
    | arm64 (aarch64) | Debian 11 | Siemens IOT2050 (IOT2050 Example Image V1.2.2), Raspberry Pi 4 (Raspberry Pi OS 11 64-bit) |
- Root permissions
- A working internet connection  

**License Requirements**
- A machine with the zenon License Manager installed and an available network license
- Make sure to have a suitable license with the desired functionality (drivers, process gateways) for the intended use case.
- This machine acts as license server and the license needs to be shared for network access. Therefore this machine must be accessible from the Linux machine in order to retrieve a license.
- See [zenon Online Help](https://onlinehelp.copadata.com/) in the licensing section for more information on this topic

**IIoT Services installation**
- An optional installation of the zenon IIoT Services version 12.0 or later is recommended and used in this guide.
- It does not matter if the installation is done using Windows or Docker.  
- Have a look at the [zenon Online Help](https://onlinehelp.copadata.com/) and the *Getting Started Guide for zenon IIoT Services*.

**Engineering Studio**
- Your engineering machine running Windows and zenon Engineering Studio 12
- Ensure to have the Device Management Interface Components Version 12 installed, which are available on the zenon 12 installation media.


# Installation steps

The following steps will guide you step-by-step through the installation procedure.  
1. Login to the Linux machine by means of a physical keyboard, mouse and display, or just connect to it using a remote shell using ssh. Typically a user account with root permissions is required to perform the installation steps.  

## Setup the remote APT repository
1. Download and install the repository's gpg key  
`wget -O- https://repository.copadata.com/zenon/12/release/copadata-archive-keyring.gpg.key | gpg --dearmor | sudo tee /usr/share/keyrings/copadata-archive-keyring.gpg > /dev/null`
2. Add the APT repository to the list of remote repositories and ensure to use the correct architecture for your hardware configuration  
    - Command for **amd64:**  
        `echo "deb [arch=amd64 signed-by=/usr/share/keyrings/copadata-archive-keyring.gpg] https://repository.copadata.com/zenon/12/release/ jammy main" | sudo tee /etc/apt/sources.list.d/copa-data.list`  
    - Command for **arm64:**  
        `echo "deb [arch=arm64 signed-by=/usr/share/keyrings/copadata-archive-keyring.gpg] https://repository.copadata.com/zenon/12/release/ bullseye main" | sudo tee /etc/apt/sources.list.d/copa-data.list`  
3. Update the local package index files for the repository  
   `sudo apt update`



## Install service-engine package
The installation procedure is carried out on the command line interface and is valid for both architectures.

1. Install the Service Engine package  
    `sudo apt install service-engine`  
    This will install the Service Engine and all required software packages on your system.  

**Hint:** Ensure to also let the system install the packages `locales-all` and `iiot-cli-12`, as they are required for the flawless functionality of the Service Engine. Those packages are installed by default along with the `service-engine` package.

## Configure a network license

- Open the file `/etc/copa-data/License.ini` using `nano` or another text editor and insert the following configuration with the appropriate *license serial number* and *license server*.  
    `sudo nano /etc/copa-data/License.ini`  
    *Hint: File names on Linux are case sensitive.*  
 
    ``` ini
    [Runtime]
    SERIAL0 = <license serial number>
    SERIAL0_LOCATION = <machine name of the license server>

    [LogicRuntime]
    SERIAL0 = <license serial number>
    SERIAL0_LOCATION = <machine name of the license server>

    [ProcessGateway]
    SERIAL0 = <license serial number>
    SERIAL0_LOCATION = <machine name of the license server>
    ```


## Download and install the CA certificate of the IIoT Services

**Explanation:**  
The following steps are only important if you use the default HTTPs certificate or any other self-signed HTTPs certificate with the IIoT Services. As the default IIoT Services HTTPs certificate is not trusted, you need to add the respective CA certificate to the list of trusted certificates.  

**Verification:**  
If your IIoT Services installation already uses a trusted certificate, you can ignore this chapter. You can verify if the HTTPs certificate is trusted with the following command `curl 'https://<url-of-iiot-services>:<port-of-iiot-services>'`. If the command works without any certificate error, the Linux machine already trusts the certificate of the IIoT Services.

If the certificate is not trusted, follow these steps:
1. Open the Service Configuration Studio of the IIoT Services and download the CA certificate to your machine.
2. Transfer the CA certificate to the Linux machine using an appropriate mechanism like `scp`, `ftp` or network file shares. [Link to SCP Manpages](https://manpages.ubuntu.com/manpages/trusty/man1/scp.1.html)
3. Copy the CA certificate file to `/usr/local/share/ca-certificates`. The actual name of the file does not matter.
4. Update the system's trusted CA certificates using `sudo update-ca-certificates`.  This will update the CA certificates and add the IIoT Service's certificate to the trusted list.
5. Use the curl verification command above to verify that the certificate is trusted.


## Connect the machine with the Device Management service
The Device Management service allows the transfer of zenon projects to connected devices.
Detailed information about the provided functionalities can be found in the [zenon Online Help](https://onlinehelp.copadata.com/).

To connect the machine with the Device Management, follow these steps:
1. Setup the device agent and register the machine with the Device Management  
    `iiot-cli setup-agent -u https://<url-of-iiot-services>:<port-of-iiot-services> --use-device-code`  
2. Follow the instructions and open the Identity Service in a web browser to authenticate with your IIoT Services administrator account. The registration of the device will automatically continue as soon as you are authenticated and have accepted the CLI grant request shown in the Identity Service.  
3. Verify the connection status of the device in the Device Management's UI by opening the Service Configuration Studio.  
    The device needs to be in state *Online*  
    You can also verify the state of the device management service using `sudo systemctl status device-agent.service`

**Hint:** If your Linux device does offer a user interface and web browser, you can omit the `--use-device-code` argument. In this case it will directly open a web browser on the same machine for the interactive login.  


## Transfer a zenon project using the Device Management

1. Open the zenon Engineering Studio with your desired project.  
    **Attention:** The Service Engine on Linux does not offer all functions, drivers and Process Gateways that are supported by the Service Engine on Windows. Please make sure to include only functionality and drivers in the project that are available for the Service Engine on Linux. Please refer to the [zenon Online Help](https://onlinehelp.copadata.com/).
2. Ensure to have the desired project loaded and have it configured for the IIoT Services within *Network > IIoT Services* inside the project properties.
3. Compile the project
4. Upload the project using the Device Management Wizard. (*Tools > Provide Project for Device Management*)  
    1. Enter a desired version number for the project upload  
    2. Enter the URL of the IIoT Services  
    3. Upload the project using the button `Publish`  
        A web browser will open and asks you to authenticate yourself via the Identity Service.
5. Open the Device Management UI in the web browser and verify that the project was uploaded successfully.
6. Switch to the deployment tasks overview and create a deployment task for your Linux Service Engine  
    1. Untick the checkbox *Use device-specific Project ID* when you create the deployment task.  
    2. Select the deployment type *instant*
    3. Create the deployment task.
7. Open the logs to see the progress of the deployment task
8. Once the deployment task has finished successfully, you can also see with the command `sudo systemctl status serviceEngine.service` that the Service Engine has restarted with the deployed project.  

**Congratulations, your Linux machine is now running the Service Engine with your zenon project.**


# Troubleshoot and How-to

In case of troubles when performing the installation and configuration steps, make sure to have a look at the troubleshooting section below.  
Reach out to support@copadata.com, if you need further assistance.

## Use Diagnosis Viewer for viewing logs
The Service Engine on Linux also includes the Log Server for viewing and retrieving log messages. To use it, open the Diagnosis Viewer on your Windows machine and connect the Diagnosis Viewer to the Log Server of the Linux machine (*File > Connect to ...*).
By default it is using the port 50780.

## Check the status of the Service Engine and the Device Agent
The status of the Service Engine and the Device Agent can be checked with the commands `sudo systemctl status serviceEngine.service` and `sudo systemctl status device-agent.service`.
It is also possible to restart them if necessary with `sudo systemctl restart <service-name>`

## Read the logs
The logs from the Service Engine or Device Agent can either be retrieved via the zenon Log Server or also via `journalctl -u <service-name>`.  
Use `journalctl -u serviceEngine.service` and `journalctl -u device-agent.service` to retrieve the respective logs.  
**You may need to scroll, to reach the desired date and time.**

## Check the license status of the Service Engine
On startup the Service Engine checks for an available license. If no license server and/or license serial number is configured, the Service Engine won't start up properly. In this case, this can be checked via `sudo systemctl status serviceEngine.service`. Also make sure to provide a valid license serial number and license server via the file `/etc/copa-data/License.ini`.
