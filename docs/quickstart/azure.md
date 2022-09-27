## Requirements 
To get started running Coder on Azure, you will need an Azure account with the capability to create new Virtual Machines. 

## Create An Azure VM

From the Azure Portal, navigate to the Virtual Machines Dashboard. Click Create, and select creating a new Azure Virtual machine .

<img src="../images/quickstart/azure/azure1.jpg">

This will bring you to the `Create a virtual machine` page. Select the subscription group of your choice, or create one if necessary.

Next, name the VM something relevant to this project using the naming convention of your choice. Change the region to something more appropriate for your current location. For this tutorial, we will use the base selection of the Ubuntu Gen2 Image and keep the rest of the base settings for this image the same.

<img src="../images/quickstart/azure/azure2.png">

<img src="../images/quickstart/azure/azure3.png">

Up next, under `Inbound port rules` modify the Select `inbound ports` to also take in `HTTPS` and `HTTP`.

<img src="../images/quickstart/azure/azure4.png">

The set up for the image is complete at this stage. Click `Review and Create` - review the information and click `Create`. A popup will appear asking you to download the key pair for the server. Click `Download private key and create resource` and place it into a folder of your choice on your local system. 

<img src="../images/quickstart/azure/azure5.png">

Click `Return to create a virtual machine`. Your VM will start up! 

<img src="../images/quickstart/azure/azure6.png">

Click `Go to resource` in the Virtual machine and copy the public IP address. You will need it to SSH into the virtual machine via your local machine.

Follow [these instructions](https://learn.microsoft.com/en-us/azure/virtual-machines/linux-vm-connect?tabs=Linux) to SSH into the virtual machine. Once on the VM, you can run and install Coder using your method of choice. For the fastest install, we recommend running Coder as a system service.

## Install Coder

For this instance, we will run Coder as a service command, however you can run Coder a multitude of different ways. You can learn more about those [here](https://coder.com/docs/coder-oss/latest/install).

In the Azure VM instance, run the following command to install Coder

```sh
curl -fsSL <https://coder.com/install.sh> | sh
```

## Run Coder

First, edit the `coder.env` file to enable `CODER_TUNNEL` by setting the value to true with the following command:

```sh
sudo vim /etc/coder.d/coder.env
```
<img src="../images/quickstart/azure/azure7.png">

Exit vim and run the following command to start Coder as a system level service:

```sh
 sudo systemctl enable --now coder
```

The following command will get you information about the Coder launch service

```sh
 journalctl -u coder.service -b
```

This will return a series of Coder logs, however, embedded in the launch is the URL for accessing Coder.


Copy the URL and run the following command to create the first user, either on your local machine or in the instance terminal. 

```sh
coder login <url***.try.coder.app>
```

Fill out the prompts. Be sure to save use email and password as these are your admin username and password.

You can now access Coder on your local machine with the relevant  `***.try.coder.app` URL and logging in with the username and password.

## Creating and Uploading Your First Template

First, run `coder template init` to create your first template. You’ll be given a list of possible templates to use. This tutorial will show you how to set up your Coder instance to create a Linux based machine on Azure. 

<img src="../images/quickstart/azure/azure9.png">

Press `enter` to select `Develop in Linux on Azure` template. This will return the following: 

<img src="../images/quickstart/azure/azure10.png">

To get started using the Azure template, install the Azure CLI by following the instructions [here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt). Run `az login` and follow the instructions to configure the Azure command line. 

For this example, Coder was run as a system level service, which creates the system user `coder` for handling processes. The Coder user will require access to the Azure credentials to initialize the template.

Run the following commands to copy the Azure credentials and give the `coder` user access to them:

```sh
sudo cp -r ~/.azure /home/coder/.azure 
sudo chown -R coder:coder /home/coder/.azure/
```

Navigate to the `./azure-linux` folder where you created your template and run the following command to put the template on your Coder instance. 

```sh
coder templates create
```

Congrats! You can now navigate to your Coder dashboard and use this Linux on Azure template to create a new workspace!

## Troubleshooting

If you’re not sure if Coder is running correctly as a system service, use the command `systemctl status coder` to show the status of the Coder process.

To stop running Coder as a system service, run the following command: `systemctl stop coder`. 

When you run ` journalctl -u coder.service -b`, there’s a chance that it may not show any logs, preventing you from seeing the url for the running Coder instance. Use the command `systemctl stop coder` to stop the Coder system service, and run the command to start Coder as a system service again. You can also use the command `systemctl restart coder` to quickly restart the service. 