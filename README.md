# Programmatic-Remediation-in-Linux-BASH-Scripting-

I will demonstrate how to run an authenticated scan on a device through tenable.io and then pragmatically remediate all issues.

<h2>Requirements for this Lab</h2>

- <b> Microsoft Azure </b>
- <b> Ubunto Virtual Machine </b>
- <b> Tenable.IO </b>

<p align="center">
Today, I will run an authenticated scan against a device on the network and pragmatically apply all necessary remediations. The purpose of today's lab is to demonstrate how to create scans, run them, and then remediate the issues found in those scans using scripts. 

## I will log in to Azure so I can create my Ubuntu virtual machine first. 

VM has been created.

<img src="https://i.imgur.com/POm1IAm.png" height="80%" width="80%" alt="Agent Group created"/>

## Now, I will log into Tenable to create the vulnerability scan.

### The scan I will be running will be based on the User-Defined Template (Linux DISA STIG).

All I will have to do is select the template, make sure I put the local scan engine, and add the VM's private IP Address to it. For this exercise, the Private IP is ```10.1.0.110```. 

After that, I will have to add the credentials I used to provision the VM into the scan settings so it does a full authenticated scan. I also needed to make sure to include that the elevated privileges are on, and the user for that is ```root```.

### I have successfully launched the scan. 

<img src="https://i.imgur.com/tWmGcrY.png" height="80%" width="80%" alt="Agent Group created"/>

### I have to wait about 20-30 minutes for the scan results, but in the meantime, I have logged into the VM via Bastion Host.

<img src="https://i.imgur.com/Rqnm1Zr.png" height="80%" width="80%" alt="Agent Group created"/>

### The baseline scan has been completed. Results can be viewed [Here](https://github.com/goubx/Programmatic-Remediation-in-Linux-BASH-Scripting-/blob/main/Initial%20Scan.pdf).

The initial baseline results were:
- 1 Critical
- 0 High
- 1 Medium
- 1 Low 

## Now I will add vulnerabilities to the VM via SSH. 

### The first vulnerability I will be adding is enabling SMBv1

The steps to doing are:

**Step 1 - Install SMBv1 Server**

```bash
# Update the local package index from configured repositories
sudo apt update
```

```bash
# Install the Samba file-sharing service without prompting for confirmation
sudo apt install samba -y
```

**Step 2 - Configure the configuration file** 

```bash
# Open the Samba configuration file in the nano text editor with admin privileges
sudo nano /etc/samba/smb.conf
```
Go to the very bottom and paste:
[global]
server min protocol = NT1

Save:
CTRL+X → Y → ENTER

**Step 3 - Restart SMB and Enable it** 

```bash
# Restart the Samba (SMB) service to apply configuration changes
sudo systemctl restart smbd
```
```bash
# Enable the Samba (SMB) service to start automatically at boot
sudo systemctl enable smbd
```
**Step 4 - Verify** 

```bash
# Show which process is listening on port 445 (SMB); filter output for that port
sudo ss -tulpn | grep :445
```
I should see a listen entry.

### The second vulnerability is an Anonymous FTP enabled. 

**STEP 1 — Install FTP Server**

```bash
# Update the local package index from configured repositories
sudo apt update
```

```bash

# Install the vsftpd FTP server without prompting for confirmation
sudo apt install vsftpd -y
```
**STEP 2 — Enable Anonymous Access**

```bash
# Open the vsftpd config file in the nano text editor with admin privileges
sudo nano /etc/vsftpd.conf
```
I must find:

```anonymous_enable=NO```

Change to:

```anonymous_enable=YES```

If I didn’t see it, I would've added at the bottom:

```anonymous_enable=YES```


Save:
CTRL+X → Y → ENTER

<img src="https://i.imgur.com/czFKtxu.png" height="80%" width="80%" alt="Agent Group created"/>

As you can see, the 2nd vulnerability was added.

### Now I need to verify the FTP Port

I will enter:
```bash
# Show which process is listening on port 21 (FTP); filter the output for that port
sudo ss -tulpn | grep :21
```
<img src="https://i.imgur.com/h4LxDzF.png" height="80%" width="80%" alt="Agent Group created"/>

As you can see above, it is listening.

### As a precaution, I will try to connect to the FTP server from the VM to confirm it works.

I entered:
```bash
# Connect to the FTP server running on your own machine (localhost)
ftp localhost
```
username: ```anonymous```
password: ```anything```

If it logs in, that means the vulnerability exists.

<img src="https://i.imgur.com/qKwwF2m.png" height="80%" width="80%" alt="Agent Group created"/>

I was logged in, which proves that the vulnerability exists. I used control Z to exit.

### Now its time for me to run another scan and view the results.

## The second scan is now complete. Results can be viewed [here](https://github.com/goubx/Programmatic-Remediation-in-Linux-BASH-Scripting-/blob/main/Second%20Scan.pdf)

As you can see, the vulnerabilities we added from:

<img src="https://i.imgur.com/TLDDJZB.png" height="80%" width="80%" alt="Agent Group created"/>

As you can see, new vulnerabilities have been added, such as:

- Anonymous FTP Enabled
- SMB Signing not required

## Now I'm going to use my bash scripts to remediate the vulnerabilities.

**Step 1: Disable SMBv1**

I ran this command:

```bash
wget https://raw.githubusercontent.com/kenbananola/ken-remediation-scripts/refs/heads/main/automation/remediation-enable-smb-signing.sh && chmod +x ./remediation-enable-smb-signing.sh && ./remediation-enable-smb-signing.sh
```

Verify it:

```bash
testparm -s | grep "server signing"
```
<img src="https://i.imgur.com/gWOSRtM.png" height="80%" width="80%" alt="Agent Group created"/>

**Step 2: Remediate Anonymous FTP**

I ran this command:

```bash
wget https://raw.githubusercontent.com/kenbananola/ken-remediation-scripts/refs/heads/main/automation/remediation-disable-anon-ftp.sh && chmod +x ./remediation-disable-anon-ftp.sh && ./remediation-disable-anon-ftp.sh
```

I verified it with:

```bash
grep anonymous_enable /etc/vsftpd.conf
```

I knew it was successful because it showed ```anonymous_enable=NO```.

<img src="https://i.imgur.com/PwWbvWN.png" height="80%" width="80%" alt="Agent Group created"/>


### Now I will run another scan to verify that vulnerabilities are gone and that the VM is back to its baseline.

### The results from the final scan are in, and the two main vulnerabilities have been removed. You can view the final scan results [here](https://github.com/goubx/Programmatic-Remediation-in-Linux-BASH-Scripting-/blob/main/Final%20Scan%20Results.pdf)

