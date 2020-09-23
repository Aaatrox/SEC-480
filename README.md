# SEC-480 Tech Journal

## Milestone Bare Metal 1

### IPMI & ESXi Configuration

#### Mounting Virtual Media on IPMI

Click **Virtual Media**<br />
Select **CD-ROM Image** from the dropdown menu<br />
Fill out the fields with the appropriate information<br />
*(Make sure the image file has filename extension attached to the end of the path for the **Path to Image** field)*<br />
Click **Mount** and **Refresh Status** to ensure the image has been mounted<br />

#### Selecting a New IPMI boot image

Restart the system<br />
Press **F11** using the virtual keyboard on the second SuperMicro start logo to invoke the boot menu<br />
Select the mounted image file from the menu (**Virtual CDROM**)<br />
Continue through the installation selecting default options, with the exception of:
* Choosing the desired storage device
* Upgrading or installing ESXi based on user preferences for VMFS datastore<br />
Create a strong password and record it *(cannot be recovered if forgetten)*

#### Configuring IPMI Network Settings

On the default IPMI homescreen, hit **F2** on the virtual keyboard to open settings<br />
Navigate to **Configure Management Network** and hit enter<br />
Navigate to **IPv4 Configuration** and hit enter<br />
Select the **Static IPv4 address** option and enter the given IP address, subnet mask, and default gateway<br />
Hit enter to return to the network settings<br />
Navigate to **DNS Configuration** and hit enter<br />
Select the second option to enter in DNS addresses and the hostname manually<br />
Hit enter once those are filled out<br />
Navigate to **Custom DNS Suffixes** and edit that accordingly (cyber.local)<br />
Hit escape and Y to apply the changes made<br />
Hit escape one more time to return to the IPMI homescreen to ensure changes were applied

#### Accessing the VMWare UI

Enter the URL given on the IMPI homescreen into a browser<br />
Sign in using **root** and the password set during the installation

#### Creating a New Datastore in VMWare

Click **Storage** in the menu on the left<br />
Click **New Datastore**<br />
Select **Create new VMFS datastore** and hit next<br />
Select the desired storage device, name the datastore, and hit next<br />
Use th default allocation options and hit next<br />
Review the settings and hit finish
