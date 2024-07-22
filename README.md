# SAP-SIOS-HighAvailability-ASCS-ERS-HANADB
SAP-SIOS-HighAvailability-ASCS-ERS-HANADB
SIOS LifeKeeper for High Availability of SAP ASCS /ERS HA and HANA Database on Azure
Introduction
SAP HANA is a high-performance in-memory database that supports real-time analytics and transactions. Enterprises widely use it to run their critical business applications and processes. However, SAP HANA systems also require high availability and disaster recovery solutions to ensure business continuity and data protection in case of failures or disasters.
Azure is a cloud platform offering various services and features to host, manage, and scale SAP HANA systems. Azure provides high-performance virtual machines, storage, networking, and security options that can meet the requirements of SAP HANA workloads. However, Azure does not offer a native high-availability solution for SAP HANA systems, which means that customers need to use third-party tools or custom scripts to achieve high availability for their SAP HANA databases on Azure.
SIOS LifeKeeper is a proven, high-availability disaster recovery solution that can protect SAP HANA systems on Azure. It can monitor, detect, and recover from failures of SAP HANA instances, virtual machines, storage, network, or Azure services. In addition, this software can also replicate data between primary and secondary SAP HANA systems and perform automatic or manual failover and failback operations. SIOS LifeKeeper can integrate with Azure services such as Azure Load Balancer, Azure Traffic Manager, and Azure Site Recovery to provide a comprehensive and reliable high-availability solution for SAP HANA systems on Azure.
Prerequisites
Before you can configure and deploy a highly available SAP HANA system on Azure using SIOS LifeKeeper, you need to meet the following prerequisites:
●	An Azure subscription and an Azure account with sufficient permissions to create and manage resources.
●	SAP HANA license and installation media.
●	SIOS LifeKeeper license and installation media.
●	Two or more Azure virtual machines that meet the SAP HANA hardware and software requirements. You can use the Azure Marketplace to create SAP HANA certified virtual machines.
●	shared storage solution that supports SAP HANA data and log volumes. You can use Azure NetApp Files, Azure Files, or Azure Shared Disks as shared storage options.
●	A network solution that allows communication between the SAP HANA systems and the SIOS LifeKeeper nodes. You can use Azure Virtual Network, Azure VPN Gateway, or Azure ExpressRoute as network options.
●	A load balancer solution that can distribute traffic to the SAP HANA systems and the SIOS LifeKeeper nodes. You can use Azure Standard Load Balancer.
Configuration Steps
After you have prepared the prerequisites, you can follow these steps to configure and deploy a highly available SAP HANA system on Azure using SIOS LifeKeeper:
1.	Install SAP HANA on the primary virtual machine and configure the SAP HANA data and log volumes on the shared storage.
2.	Install SAP ASCS on the primary virtual machine, SAP ERS on the Secondary Virtual Machine, and /sapmnt on the shared storage.
3.	Install SIOS LifeKeeper on the primary virtual machine and join it to a SIOS LifeKeeper cluster.
4.	Create a SIOS LifeKeeper SAP HANA resource hierarchy on the primary virtual machine and configure the SAP HANA instance details and the shared storage paths.
5.	Install SAP HANA on the secondary virtual machine and configure the SAP HANA data and log volumes on the shared storage.
6.	Install SIOS LifeKeeper on the secondary virtual machine and join it to the same SIOS LifeKeeper cluster as the primary virtual machine.
7.	Extend the SIOS LifeKeeper SAP HANA resource hierarchy from the primary virtual machine to the secondary virtual machine and configure the replication mode and the failover policies.
8.	Configure the load balancer to direct traffic to the SAP HANA systems and the SIOS LifeKeeper nodes.
9.	Test the high availability and disaster recovery scenarios by simulating failures and verifying the failover and failback operations.

Architecture Design

 

SIOSLV-RG
IP address Assignment
Component	West US 2	IP-ADDRESS
VNET	vnet-sioslv-westus2	10.3.0.0/16
Snet-app-sioswestus2	snet-app-siosz1-westus2	10.3.0.0/24
Snet-hdb-sios-westus2	snet-app-siosz2-westus2	10.3.1.0/24
s4hdiagsios
		
Azure File share (NFS 4.1)	s4hsiosnfstest	
AzureFirewallSubnet	AzureFirewallSubnet	
AzureBastionSubnet	AzureBastionSubnet	10.3.2.0/26
Azure File share nfs4.1	s4hsiosfsdiag	
ASCS Cluster Node1	vmslvascs1	10.3.0.4
ASCS Cluster Node2	vmslvascs2	10.3.0.5
Witness server for SAP in Zone3	vmslvwit1	10.3.0.6
SAP PAS 	vmslvpas	10.3.0.7
VIP-SAP DB	S4sascs
s4sers	10.3.0.20
10.3.0.21
VIP-HANA DB	sioshdb-lb	10.3.1.10
HANA DB Cluster Node 1	vmslvhdb1	10.3.1.4
HANA DB Cluster Node 2	vmslvhdb2	10.3.1.5

Created sioslv-rg
Created VNET/SN 


SUSE SLES for SAP Applications 15 SP5 with 24x7


Installation of HANA DB on node1 and node2
 

 

 
 

Please perform the same for Node2

 



Download the software
 
 

 


Download lkwmc

 



./setup
Install license key

 

 

 


We don’t need to select  Likekeeper startup

 

Setup failed due to 
 

 


 

Mistyped the location of license key

 

 


Now perform the same step on VM2

vmslvhdb2:/misc/SIOS-9.8.1/siossoft981 # mount /misc/SIOS-9.8.1/sps.img /misc/SIOS-9.8.1/siossoft981 -t iso9660 -o loop


 


 

vmslvhdb2:/misc/SIOS-9.8.1/siossoft981 # suseconnect -p sle-module-legacy/15.5/x86_64
Registering system to registration proxy https://smt-azure.susecloud.net

Updating system details on https://smt-azure.susecloud.net ...

Activating sle-module-legacy 15.5 x86_64 ...
-> Adding service to system ...
-> Installing release package ...

Successfully registered system
 


Install LKWMC 

 

WITNESS SERVER

 

 

 

Prerequisite :
vi  /etc/default/LifeKeeper
NOBCASTPING=1




Now browse the LKWMC via link below for 


vmslvhdb1:/misc/SIOS-9.8.1/lkwmc/sios-lkwmc-1.1.0 # /opt/LifeKeeper/bin/lcdstatus -q
LifeKeeper does not appear to be running.
vmslvhdb1:/misc/SIOS-9.8.1/lkwmc/sios-lkwmc-1.1.0 # ./lkstart
-bash: ./lkstart: No such file or directory
vmslvhdb1:/misc/SIOS-9.8.1/lkwmc/sios-lkwmc-1.1.0 # /opt/LifeKeeper/bin/lkstart
Created symlink /etc/systemd/system/lifekeeper-graphical.target.requires/lifekeeper.service → /usr/lib/systemd/system/lifekeeper.service.
Created symlink /etc/systemd/system/lifekeeper-multi-user.target.requires/lifekeeper.service → /usr/lib/systemd/system/lifekeeper.service.
vmslvhdb1:/misc/SIOS-9.8.1/lkwmc/sios-lkwmc-1.1.0 #
Broadcast message from systemd-journald@vmslvhdb1 (Thu 2024-04-11 19:00:22 UTC):

lcdinit[5788]: EMERG:lcd.lcdchkseml:::011138:The LifeKeeper product on this system is using an evaluation license key which will expire at midnight on 05/11/24. To continue functioning beyond that time, a permanent license key must be obtained.

vmslvhdb1:/misc/SIOS-9.8.1/lkwmc/sios-lkwmc-1.1.0 # /opt/LifeKeeper/bin/lcdstatus -q
LOCAL      TAG   ID    STATE     PRIO  PRIMARY
vmslvhdb1:/misc/SIOS-9.8.1/lkwmc/sios-lkwmc-1.1.0 #

Now launch the LKWCM again

Login as root/pa..
To connect to the LifeKeeper Web Management Console from a web browser:
    https://vmslvhdb1:5110

 

 

 

Create communication between all systems
 

Click add new server to add vmslvhdb2
 
 
 

Click create another communication path to Witness server

 

 

 


Create communication between vmslvhdb2 to vmslvwit1

 

 

 

 


IP
Click on Resource tree for creating the resource IP
`
 

 

 
 


 

Click extend 
 
 

 

 

 

Click to create the resource for  – LB health checkup
 

 
 

 
 
 

click Extend
 
 

 

 

 
Before creating the below resource, enable HSR
Take Backup of SYSTEMDB and TENANT DB on both nodes
Copy the below ssfs files to the secondary HDB 
s4sadm@vmslvhdb1:/usr/sap/S4S/SYS/global/security/rsecssfs> ls
data  key

s4sadm@vmslvhdb1:/usr/sap/S4S/SYS/global/security/rsecssfs> scp -rp /usr/sap/S4S/SYS/global/security/rsecssfs/* s4sadm@vmslvhdb2:/usr/sap/S4S/SYS/global/security/rsecssfs/
s4sadm@vmslvhdb2's password:
SSFS_S4S.DAT                                                                                                                                                                                             100% 2960   855.0KB/s   00:00
SSFS_S4S.KEY                                                                                                                                                                                             100%  187   127.9KB/s   00:00


Start the HANA system on both systems
Create the HANA resource
Enable the replication between them
s4sadm@vmslvhdb1:/usr/sap/S4S/home> hdbnsutil -sr_enable --name=s4sz1
nameserver is active, proceeding ...
successfully enabled system as system replication source site
done.
s4sadm@vmslvhdb1:/usr/sap/S4S/home>
 


hdbnsutil -sr_register --remoteHost=vmslvhdb1 --remoteInstance=00 --replicationMode=syncmem --operationMode=logreplay --name=s4sz2 –online
 

 


Creating SAP HANA resource

 

 

 

 

 
 


 

 
 

Click Close

 
Failover test on HANA DB.
Trying the takeover by right click on standby – in sync resource

 


 

 
Current status

 

SIOS cluster will detect and start it  and reregister it as a secondary
 

Failback

 

 


Now let's work on SAP ASCS / ERS 
NFS shares 
/sapmnt
/ASCS00
/ERS01
/SYS

Local 
/usr/sap

 

Goto SAP system and mount them
sudo mkdir -p /usr/sap/S4S/ascs00
sudo mount -t nfs siossapnfs.file.core.windows.net:/siossapnfs/ascs00 /usr/sap/S4S/ascs00 -o vers=4,minorversion=1,sec=sys,nconnect=4

sudo mkdir -p /usr/sap/S4S/ers01
sudo mount -t nfs siossapnfs.file.core.windows.net:/siossapnfs/ers01 /usr/sap/S4S/ers01 -o vers=4,minorversion=1,sec=sys,nconnect=4

sudo mkdir -p /sapmnt/S4S
sudo mount -t nfs siossapnfs.file.core.windows.net:/siossapnfs/sapmnt /sapmnt -o vers=4,minorversion=1,sec=sys,nconnect=4

sudo mkdir -p /usr/sap/S4S/sys
sudo mount -t nfs siossapnfs.file.core.windows.net:/siossapnfs/sys / -o vers=4,minorversion=1,sec=sys,nconnect=4

 

 

Lets mount and install SIOS

Prerequisite
vmslvascs1:~ # suseconnect -p sle-module-legacy/15.5/x86_64
Registering system to registration proxy https://smt-azure.susecloud.net
Updating system details on https://smt-azure.susecloud.net ...
Activating sle-module-legacy 15.5 x86_64 ...
-> Adding service to system ...
-> Installing release package ...
Successfully registered system
 
vmslvascs1:/misc/SIOS-9.8.1 # ls
lkwmc  siossoft981  sps.img
vmslvascs1:/misc/SIOS-9.8.1/siossoft981 # mount /misc/SIOS-9.8.1/sps.img /misc/SIOS-9.8.1/siossoft981 -t iso9660 -o loop
mount: /misc/SIOS-9.8.1/siossoft981: WARNING: source write-protected, mounted read-only.

vmslvascs1:/misc/SIOS-9.8.1/siossoft981 # ./setup

 

 
 
 
 

 

 
 
Install LKGUI
 
To connect to the LifeKeeper Web Management Console from a web browser:
    https://vmslvascs1:5110

vmslvascs1:/misc/SIOS-9.8.1/lkwmc/sios-lkwmc-1.1.0 # /opt/LifeKeeper/bin/lcdstatus -q
LOCAL       TAG   ID    STATE     PRIO  PRIMARY


Now Install the LifeKeeper on vmslvascs2 the same as above.
 

 
Install LifeKeeper Web Management Console (LKWMC) 

 

To connect to LKWMC from a web browser:
    https://vmslvascs2:5110


Now let's launch the LK wmc and setup communication between resources and create LB 
 
 

Click communication path and under operations – create communication path
 

Click ADD A NEW SERVER
 

It will ask for login info of vmslvascs2
 

 

Now create another communication between
Vmslvascs1🡪vmslvascs2 -- Done
Vmslvascs1🡪vmslvwit1
Vmslvascs2 🡪 vmslvwit1
 

Now let's create the resources
IP
LB Health Checkup
SAP
IP
 

10.3.0.20

 

 
 

 

Click Extend
 

 

 

 
Click Close
 

Let's add ERS IP
 
 

 


 



 
 

 

 

Click Close

 

Now add LB Health Check for SAPASCS / SAPERS
 


 

 
 


 
 

 
 

 

Now perform for  vmslvascs2 from vmslvascs1 





 


 

 
 

 
 

 
 

Now install SAP ASCS 
Ping virtual ips
10.3.0.20      s4sascs
10.3.0.21      s4sers
vmslvascs1:/misc/s4hana2023-includesall/SWPM # ping -a 10.3.0.20
PING 10.3.0.20 (10.3.0.20) 56(84) bytes of data.
64 bytes from 10.3.0.20: icmp_seq=1 ttl=64 time=0.034 ms
64 bytes from 10.3.0.20: icmp_seq=2 ttl=64 time=0.033 ms


./sapinst SAPINST_USE_HOSTNAME=s4sascs
 

 


 

 

 

 

 

 


 



 

ERS on node2

vmslvascs2:/misc/s4hana2023-includesall/SWPM # ./sapinst SAPINST_USE_HOSTNAME=s4sers
[==============================] | extracting...  done!
INFO: Native locale set to C.UTF-8
INFO       2024-04-22 21:44:45.283 (mainThread) [sixxcreate.cpp:349]
********************************************************************************
Initial log directory: /root/.sapinst/vmslvascs2/13944
********************************************************************************

WARNING    2024-04-22 21:44:45.284 (root/root) (startInstallation) [syxxcnwmgt.cpp:61] id=syslib.network.overriddenHostName void CSyNetworkMgtImpl::overrideHostName(const iastring & "

https://vmslvascs2:4237/sapinst/docs/index.html

 

 

 

 

/usr/sap/S4S/SYS

/usr/sap/S4S/SYS

 

Installing PAS


 

vmslvpas:/usr/sap/s4hana2023-includesall/SWPM # ./sapinst
 

 

 



 
 
 

 

 

Now install PAS on same server

 
 


 


 

 

 


 

 


 
Creating a HA cluster for your SAP ASCS/ERS servers in AWS - Google Docs
Copy the sapservices file between node1 and node2
vmslvascs2:/opt/LifeKeeper/bin # more /usr/sap/sapservices
systemctl --no-ask-password start SAPS4S_01 # sapstartsrv pf=/usr/sap/S4S/SYS/profile/S4S_ERS01_s4sers
systemctl --no-ask-password start SAPS4S_00 # sapstartsrv pf=/usr/sap/S4S/SYS/profile/S4S_ASCS00_s4sascs

Also perform below - Verify that the rules for both your ASCS and ERS instances are in the /etc/polkit-1/rules.d/ directory.  If not copy the ERS file to the ASCS server and vice versa:

 

 



 
Make sure you copy the files between system
/etc/systemd/system/
SAPS4S_00.service
SAPS4S_00.service.d 
SAPS4S_01.service
SAPS4S_01.service.d 










Let's create the SAP resource for ASCS/ERS on SIOS 

 


 
 

 

 
 

 

 

 

Now create ERS resource
 
 

 

 

 
 

 

 

 

Create dependency 
SAP as parent resource 
Child as VIP 
Child as LBHC
 

Confirm
 

Confirm

===Now for SAP ERS  resource
 



 

 


Lets delete the old license
vmslvascs2:/opt/LifeKeeper/bin # cd /var/LifeKeeper/license/
vmslvascs2:/var/LifeKeeper/license # ls
20240422.lic  20240429.lic
vmslvascs2:/var/LifeKeeper/license # ls -ltr
total 16
-rw-r--r-- 1 root root 6121 Apr 22 15:19 20240422.lic
-rw-r--r-- 1 root root 6121 Apr 29 14:02 20240429.lic
vmslvascs2:/var/LifeKeeper/license # rm 20240422.lic


Let’s crash node1

vmslvascs1:/etc/systemd/system # echo c > /proc/sysrq-trigger

Node1 is hanging 
ASCS moved to Node2 and the locks are still there and SAP can connect without any issue.



 

After a few seconds, the VM1 did not come up, and we see the ERS is failing over to Node1
Restarted the VM manually –once the VM is up and running 
SIOS will automatically move the ERS to another node; I see the status as below
 

Manually switchover the ASCS to Node1

Right Click and make it as in service


 

 



 

 



 

Done: Tests are successful.
