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

### vSwitch & ISO Directory Configuration

#### Creating a Datastore Directory & Uploading Files
Click **Storage** in the menu on the left<br />
Open the desired datastore and click **Datastore browser**<br />
Click **Create directory**<br />
Name the directory and click **Create directory**<br />
Highlight the new directory and click **Upload**<br />
Upload the desired file(s)

#### Creating Virtual Switches & Port Groups
Click **Networking** in the menu on the left<br />
Select **Virtual switches** and click **Add standard virtual switch**<br />
Name the vSwitch, delete the uplink if necessary, and click add<br />
Select **Port groups** and click **Add port group**<br />
Name the port group, assign it to desired vSwitch, and click add

#### Creating Virtual Machines
Right-click **Virtual Machines** in the menu on the left and select **Create/Register VM**<br />
Highlight **Create a new virtual machine** and click next<br />
Name the virtual machine, select the correct settings based on the VM, and click next<br />
Select the desire datastore to save the VM to and click next<br />
Edit the settings based on the VM, add hard disks and network adapters if necessary<br />
Select **Datastore ISO file** from the **CD/DVD Drive** dropdown menu and navigate to the desired ISO file<br />
*(Make sure to check all the desired **Connect** boxes to ensure components are connected)*<br />
Click next, review VM settings and preferences, and click finish

#### VyOS Installation & Configuration
Ensure only 2 network adapters are connected prior to booting VM *(delete any additional adapters in VM settings)*<br />
Ensure that the **Guest OS Version** is **Debian GNU/Linux 10 (64 Bit)**<br />
Save any changes, boot the VM, and select the first option on startup<br />
```
vyos@vyos:~$ install image
```
Select defaults for installation and reboot after installation is complete<br />
Clear the network interfaces:
```
vyos@vyos:~$ delete interfaces ethernet eth0 hw-id
vyos@vyos:~$ delete interfaces ethernet eth1 hw-id
vyos@vyos:~$ commit
vyos@vyos:~$ save
vyos@vyos:~$ set interfaces ethernet eth0 address dhcp
vyos@vyos:~$ commit
vyos@vyos:~$ save
```
Hard shutdown the VM *(to ensure new IPs aren't assigned)* and take a base snapshot for VyOS<br />
Start up the VM and check to see if DHCP address was assigned using:
```
vyos@vyos:~$ show interfaces
```
Now comes the actual configuration of our VyOS system:
```
$ configure
# set system host-name <hostname>
(i.e. # set system host-name 480-fw10)
# set protocols static route 0.0.0.0/0 next-hop 192.168.3.250
# delete interfaces ethernet eth0 address dhcp
# set interfaces ethernet eth0 address 192.168.3.xx/24
(i.e. # set interfaces ethernet eth0 address 192.168.3.50/24)
# set interfaces ethernet eth1 address 10.0.17.2/24
# set nat source rule 10 description "NAT to CYBER"
# set nat source rule 10 outbound-interface eth0
# set nat source rule 10 source address 10.0.17.0/24
# set nat source rule 10 translation address masquerade
# set system name-server 192.168.4.4
# set system name-server 192.168.4.5
# set service dns forwarding allow-from 10.0.17.0/24
# set service dns forwarding listen-address 10.0.17.2
# set service dns forwarding system
```

#### Xubuntu Installation & Configuration
Create the VM:<br />
* Give it **3072 MB** of **Memory**<br />
* Add another **Hard disk**<br />
* Set them both to **Thin provisioned**<br />
* Set the **Network adapter** to **VM Network** *(temporarilt for updates)*<br />
* Set the **CD/DVD Media** to the Xubuntu ISO file
Power on the VM and choose the defaults for the installation<br />
Create a generic user *(champuser)* and hostname for the system<br />
Reboot the system if an option to update isn't prompted<br />
Install updates after VM has rebooted<br />
```
$ sudo apt-get install git
$ git clone https://github.com/gmcyber/480share
$ sudo -i
$ cd /home/<generic user>/480share/
($ cd /home/champuser/480share)
$ chmod +x ubuntu-sealer.sh
$ ./ubuntu-sealer.sh
$ cd ..
$ rm -rf 480share/
$ shutdown -h now
```
Change **CD/DVD Drive** to **Host device** in VM settings and save<br />
Take a base snapshot of the VM<br />
Change the **Network adapter** to **480-WAN** and start the VM *(make sure 480-fw is started prior to booting VM)*<br />
Add a new administrative user and delete the generic user<br />
Set the IP address to **10.0.17.100/24** and the gateway/DNS server to **10.0.17.2**
