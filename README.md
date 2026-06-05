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

### The baseline scan has been completed. Now I will add vulnerabilities to the VM via SSH. 

