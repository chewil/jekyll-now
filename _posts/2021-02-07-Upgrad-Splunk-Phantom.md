---
layout: post
title: Upgrade Splunk Phantom
published: true
date: 2021-02-07 16:58:00
category: Phantom
tags: Splunk Phantom admin
---
*Notes on upgrading Splunk Phantom.  

## General Instructions
https://docs.splunk.com/Documentation/Phantom/4.10.1/Install/UpgradeOverview  


# Step 1 - Full backup of Phantom  
a. https://docs.splunk.com/Documentation/Phantom/4.10.1/Admin/BackupOrRestoreOverview  
b. Run ibackup to create a full backup file  
i. /opt/phantom/bin/phenv python /opt/phantom/bin/ibackup.pyc --backup --backup-type full  
c. cd /opt/phantom/data/backup  
d. Relocate the latest full backup file created:  
i. cd /opt/phantom/data/backup  
ii. Find file:  phantom_backup_group_XX_level_0-mm_dd_YYYY-HHh_MMm_sss.tar  
With the highest XX values (group number) and it should also be at level 0.  
e. Copy the latest full backup file to /opt/phantom in case a restore is required  
i. cp phantom_backup_group_XX_level_0-mm_dd_YYYY-HHh_MMm_sss.tar /opt/phantom  

# Step 2 - Complete prerequisites
a. https://docs.splunk.com/Documentation/Phantom/4.10.1/Install  /UpgradeOverview#Prerequisites_for_upgrading_Splunk_Phantom  
b. Ensure a minimum of 5GB of space available in the /tmp directory on the Splunk Phantom instance  

3. Prepare your Splunk Phantom deployment for upgrade  
a. https://docs.splunk.com/Documentation/Phantom/4.10.1/Install/UpgradeOverview#Prepare_your_Splunk_Phantom_deployment_for_upgrade  

b. SU to the local phantom user  
i. sudo su - phantom  

c. Stop iBackup cron jobs  
i. crontab -e  
ii. Look for the following 2 jobs:  
27 1 * * 0 /opt/phantom/bin/phenv python /opt/phantom/bin/ibackup.pyc --backup --backup-type full  
27 1 * * 1-6 /opt/phantom/bin/phenv python /opt/phantom/bin/ibackup.pyc --backup --backup-type incr  
iii. Comment out both lines with # at the beginning of the line  
iv. Save file to update cron  

d. Stop all Phantom services  
/opt/phantom/bin/stop_phantom.sh  

e. SU to root  
exit  
sudo su -  

f. Clear the YUM caches  

yum clean all  

g. Update the installed software packages, excluding Nginx, and apply operating system patches. As the root user:  

yum update --exclude=nginx  

i. If kernel update was applied, then reboot server.  
reboot  
1) Wait for Phantom services to load.  
ii. If no kernel was updated, then just restart Phantom services  
1) /opt/phantom/bin/start_phantom.sh  

h. Install the Splunk Phantom repositories and signing keys  
i. Switch user to the local phantom account  
sudo su - phantom  

ii. Download the Official Unprivileged Tarball file for your operating system from the Splunk Phantom community website Product Downloads page.  

iii. Copy the installation tar file to /opt/phantom  
cp phantom-<version>.tgz /opt/phantom/  

iv. Extract tar file  
tar -xvzf phantom-<version>.tgz  

# Step 4 - Upgrade Phantom  
a. https://docs.splunk.com/Documentation/Phantom/4.9/Install/UpgradePhantomInstanceUnprivileged  
b. Stop all Phantom services  
i. /opt/phantom/bin/stop_phantom.sh  

c. Disable WAL archiving for the PostgreSQL database  
sed -i -e 's/archive_mode = on/archive_mode = off/i' /opt/phantom/data/db/postgresql.phantom.conf  

d. Start Phantom services  
i. /opt/phantom/bin/start_phantom.sh  

e. Verify proxy server configuration for the phantom user.  
i. You should be able to successfully access any web site using wget and curl  
ii. If not:  
Switch to ROOT and run the following commands:  

> echo "export http_proxy=http://webproxy.nyp.org:80/" >> /etc/profile  
> echo "export https_proxy=http://webproxy.nyp.org:80/" >> /etc/profile  
> echo "export HTTP_PROXY=http://webproxy.nyp.org:80/" >> /etc/profile  
> echo "export HTTPS_PROXY=http://webproxy.nyp.org:80/" >> /etc/profile  
> echo "export http_proxy=http://webproxy.nyp.org:80/" >> /etc/profile.d/http_proxy.sh  
> echo "export https_proxy=http://webproxy.nyp.org:80/" >> /etc/profile.d/http_proxy.sh  
> echo "export http_proxy=http://webproxy.nyp.org:80/" >> /etc/profile.d/http_proxy.csh  
> echo "export https_proxy=http://webproxy.nyp.org:80/" >> /etc/profile.d/http_proxy.csh  


iii. Exit root, then switch to phantom user  

f. Run the upgrade script  

/opt/phantom/bin/phenv /opt/phantom/phantom_tar_install.sh upgrade --without-apps  

i. Upgrade takes a LONG TIME  
ii. DO NOT WALK AWAY OR LEAVE TERMINAL WINDOW UNATTENDED  
iii. PERIODICALLY PRESS ENTER TO ENSURE YOUR SSH SESSION STAYS ALIVE WHILE UPGRADING  
iv. To monitor progress:  
	1) Open a new terminal window.  Log in then switch to the phantom user  
2) Run following command:  
	tail -f /opt/phantom/var/log/phantom/phantom_install_log  

g. If the upgrade script produced the following message:  

To improve database performance, after completing the upgrade, run: /<PHANTOM_HOME>/bin/phenv /<PHANTOM_HOME>/usr/postgresql/bin/vacuumdb -h /tmp --all --analyze-in-stages  

Then run the command:  

/opt/phantom/bin/phenv /opt/phantom/usr/postgresql/bin/vacuumdb -h /tmp --all --analyze-in-stages  

h. After upgrading phantom you must run 'phenv python3 /opt/phantom/bin/ibackup.pyc --setup' to continue using backup and restore functionality. Your current backups will be archived in the /opt/phantom/data directory.  

i. After the upgrade is complete, from Main Menu > Administration > Administration Settings > Search Settings  
i. Select Playbooks from the drop-down menu.  
ii. Then click the Reindex Search Data button.  
