---
layout: post
title: "Install Xen Orchestra from Source"
date: 2024-07-31 08:00:00 -0500
categories: XenOrchestra, XCP-NG
tags: XenOrchestra, XCP-NG
image:
 path: /assets/img/headers/XenOrchestra.png
---

# Installation Methods

1: Quick Deploy (limited feature set but quick to install)

2: From Sources (all available features for free) 


### Quick Deploy

> *Note: As mentioned above this is a limited feature set for basic usage of Xen Orchestra.*
{: .prompt-warning }

1: Navigate to the IP address of your XCP-NG host in a browser (Chrome, Firefox...anything but Safari =] )

2: Click "Quick Deploy" 

3: Provide IP address for xpng host and the root password

4: Wait for install to complete (will depend on your internet download speed)


### Install From Sources

> Reccomended method for fully featured Xen Orchestra experience. This guide will focus on installation on Debian or Ubuntu based systems.
{: .prompt-tip }

1: Using XenCenter for Windows or XO Lite (available on XCP-NG 8.3 and above. Create a Debian/Ubuntu machine and SSH into it when intall is complete. 

2: Install git
```bash
sudo apt install git
```

3: Clone repository for install/update script. <a href="https://github.com/ronivay/XenOrchestraInstallerUpdater">Link to Repository</a>
```bash
git clone https://github.com/ronivay/XenOrchestraInstallerUpdater.git

cd XenOrchestraInstallerUpdater
```

4: Copy the sample config file "sample.xo-install.cfg"
```bash
cp sample.xo-install.cfg xo-install.cfg
```

5: Setup HTTPS 
```bash
cd /etc/ssl

sudo mkdir xo

cd xo
```
```bash
sudo openssl req -newkey rsa:4096 \
            -x509 \
            -sha256 \
            -days 3650 \
            -nodes \
            -out xo.crt \
            -keyout xo.key
```
Inside the xo directory confirm you have teh xo.crt and xo.key

![IMAGE FOR DIRECTORY Listing](https://s3.us-east-1.wasabisys.com/documentationpics/XO-SSL-Directory.png)
			
			
6: Now we can edit config file in XenOrchestraInstallerUpdater directory

```bash
cd ~/XenOrchestraInstallerUpdater

nano xo-install.cfg
```
Change these 4 lines

* PORT="80" -> Port="443"

* uncomment Plugins ALL if not already done

* Uncomment lines below by removing the # in front of the line 
    * #PATH_TO_HTTPS_CERT=$INSTALLDIR/xo.crt
    * #PATH_TO_HTTPS_KEY=$INSTALLDIR/xo.key

* Change the lines above to the ssl directory we created earlier to point to our crt and key files.
    * PATH_TO_HTTPS_CERT=/etc/ssl/xo/xo.crt
    * PATH_TO_HTTPS_KEY=/etc/ssl/xo/xo.key

7: Exit nano with CTRL X and Y to save changes

8: READY TO INSTALL

```bash
sudo ./xo-install.sh
```
Select option 1 to install 

Once installation is complete navigate to the IP of your VM and use admin@admin.net and admin to login. 


![IMAGE FOR DIRECTORY Listing](https://s3.us-east-1.wasabisys.com/documentationpics/XOLoginPage.png)


### Update Xen Orchestra

1: Change into XenOrchestraInstallerUpdater directory
```bash
cd ~/XenOrchestraInstallerUpdater
```
2: Run ./xo-install.sh
```bash
sudo ./xo-install.sh
```
3: Select option 2 to update

4: Wait until update is complete