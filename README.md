# Preface

This tutorial is a complete guide to setting up a domain with folder redirection policies for users. Once implemented, user directories will be stored on some file server. For the sake of simplicity, this tutorial assumes however that you are configuring the Fileserver and the DC on the same server. You can however use very similar steps to install the fileserver on a separate server. The server in question will be referred to as "DC_NAME" throughout this tutorial. Replace this with the real name of your server whenever you come across it.

Additionally, this tutorial will deal with subjects such as file shares, which will be referred to as "SHARE_NAME", and domains which will be referred to as "DOMAIN.ORG"

# Getting started

## Installing windows server 2012 (if you haven't already)

### IMPORTANT NOTE: IF YOU HAVE ANY DATA ON THE HARD DRIVE YOU INTEND TO USE, BACK IT UP AND MOVE IT ELSEWHERE. INSTALLING WILL WIPE THE DRIVE

Create or buy a bootable disk with windows server 2012 by whatever means chosen. Boot using this disk on the chosen server. Click next, then install now. Enter the product key when prompted. Select Server with GUI when prompted then proceed. Select custom installation and clear any data from the hard drive by selecting each option then clicking delete to remove the partitions. When prompted, input a password of your choice for the administrator account. This password should be secure using a mix of upper and lower case characters, special characters and numbers.

## Basic Configuration

In order to configure the server as a domain controller, several roles need to be added to the server. Roles can be added through the server manager which should show up as soon as you log in for the first time. It may be a good idea to install updates as soon as possible also. To do this, simply open Windows update and turn on automatic updates. Next, under the Dashboard tab of the Server Manager, select "Add roles and features". Click next, then ensure that the installation type is set to "Role-based of feature-based installation". Next you will be asked to select a server from the server pool. Make sure you select the server you are currently using. If you aren't sure, you can open a command prompt and identify either the hostname of local ip address assigned to the server to verify using one of the two following commands:

```
ipconfig|findstr IPv4
```

or

```
hostname
```

Once you are sure you have selected the correct server, move on to the next page. From the list select the following

* DNS Server
* Active Directory Domain Services
* DHCP Server 

### NOTE: IN ORDER TO USE DHCP AND DNS SERVERS IT IS VERY IMPORTANT THAT YOU USE A STATIC IP ADDRESS FOR THE SERVER. THIS CAN BE CONFIGURED UNDER YOUR ROUTER SETTINGS. IF UNSURE LOOK UP A GUIDE ONLINE FOR YOUR ROUTER MODEL

Once these are all selected, move on to the next step. No specific features other than those selected should be necessary, so again move on. It is recommended that you read the notes provided for each role being installed before moving on to ensure you understand what exactly you are working with. Once you have a complete understanding, move on to the next until you reach the "Confirmation" tab. There, select "Restart the destination server automatically if required" and verify that all of the roles and features you selected are listed below. If they are, press the install button.

While this installs, take advantage of the wait time to rename the server to something more appropriate. It is safe to close the open window, so feel free to do that, then reopen the server manager. Under the "Local Server", select computer name and then click the change button. There you will see your server's name highlighted. Change this to something more appropriate for your domain controller. 

Now, the features should be installed. If this is the case you should see new tabs added to the server manager. You should now see

* Dashboard
* Local Server
* All Servers
* AD DS
* DHCP
* DNS
* File and Storage Services

Before continuing, a reboot will be required. Once the server has been rebooted, reopen server manager, and from the list, select "AD DS". The server needs to be promoted to a domain server. To do this, look to the top of the Server manager, a yellow warning box should appear, and note the message. It tells you configuration is required. Click "More..." on this warning, then click on "Promote this server to a domain controller". During the making of this tutorial, no previous domain forest was configured, so "Add a new forest" was selected. However, if a domain forest has already been configured, your steps may vary slightly. Select the option which works best for you and provide correct information, the wizard should guide you. DNS Delegation was not required at the time of writing this tutorial. Once the wizard is completed, the server should reboot to configure itself.

### NOTE: YOUR NETBIOS NAME WILL NOW BE REFERRED TO AS "DC_NAME"

Next, when the server reboots, configure DHCP under server manager the same way as above with AD DS. This time select DHCP, same warning, same steps, different wizard. The default settings should be fine.

### NOTE: THIS TUTORIAL DOES NOT USE DHCP SPECIFICALLY FOR ANYTHING HOWEVER DOMAIN CONTROLLERS SHOULD RUN AS DHCP SERVERS FOR THE CONNECTED CLIENTS TO SIMPLIFY THINGS. 

Now, open powershell or cmd, either should work and test to see if SMB is available. 

```
PS C:\Users\Administrator> net view
System error 6118 has occured.

The list of servers for this workgroup is not currently available.
```

As seen here, it is possible that you will receive an error. If this happens, open control panel and go to:
Network and Internet -> Network and Sharing center -> Change advanced sharing settings (left top) -> Turn on network discovery (for both private, and domain networks)

Sometimes this is insufficient. If this is the case, do the following:

1. Press Win+R and type services.msc
2. In the list find Computer browser and right click, then select properties. 
3. Set startup type to automatic then click apply
4. Restart the server
5. Test it again

```
PS C:\Users\Administrator> net view
Server Name 	Remark
------------------------------------------------------------------
\\\\DC_NAME
The command completed successfully.
```

It should work now. So now a share for user folders to be stored in will be required. Open File explorer, and navigate to wherever you want the folder to be (preferably the root of some drive).
