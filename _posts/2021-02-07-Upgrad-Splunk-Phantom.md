---
layout: post
title: Upgrade Splunk Phantom
published: true
date: 2021-02-07 16:58:00
category: Phantom
tags: Splunk Phantom admin
---
*Notes on upgrading Splunk Phantom as of version 4.10.1.  

# Phantom Upgrade
See the [Phantom Upgrade Overview](https://docs.splunk.com/Documentation/Phantom/4.10.1/Install/UpgradeOverview) documents to get started.  
The following is my summary of the steps required to upgrade an unprivileged (tarball) Phantom server running on RHEL7.  

# Step 1 - Full backup of Phantom  
1. Reference:  [Phantom Backup/Restore Overview](https://docs.splunk.com/Documentation/Phantom/4.10.1/Admin/BackupOrRestoreOverview)  
2. Run ibackup to create a full backup file (user: phantom)  
     `/opt/phantom/bin/phenv python /opt/phantom/bin/ibackup.pyc --backup --backup-type full`  

3. Copy the latest full backup file to /opt/phantom in case a restore is required: (user: phantom)  
    `cd /opt/phantom/data/backup`  
    `cp phantom_backup_group_XX_level_0-mm_dd_YYYY-HHh_MMm_sss.tar /opt/phantom`  
    - Where **XX** is the highest group number at level **0**.

# Step 2 - Complete prerequisites
1. Reference:  [Phantom Install](https://docs.splunk.com/Documentation/Phantom/4.10.1/Install/UpgradeOverview#Prerequisites_for_upgrading_Splunk_Phantom)  

2. Ensure a minimum of 5GB of space available in the /tmp directory on the Splunk Phantom instance  

3. Reference:  [Prepare your Splunk Phantom deployment for upgrade](https://docs.splunk.com/Documentation/Phantom/4.10.1/Install/UpgradeOverview#Prepare_your_Splunk_Phantom_deployment_for_upgrade)  

4. SU to the local phantom user and disable the iBackup cron jobs. (user: phantom)  
    `sudo su - phantom`  
    `crontab -e`  

    Look for the following 2 jobs then comment out both lines with # at the beginning of the line, then save file to update cron:
    > 27 1 * * 0 /opt/phantom/bin/phenv python /opt/phantom/bin/ibackup.pyc --backup --backup-type full  
    > 27 1 * * 1-6 /opt/phantom/bin/phenv python /opt/phantom/bin/ibackup.pyc --backup --backup-type incr  

5. Stop all Phantom services (user: phantom)  
    `/opt/phantom/bin/stop_phantom.sh`  
  
6. SU to root  
    `exit`  
    `sudo su -`  

7. Clear the YUM caches (user: root)  
    `yum clean all`  
    
8. Update the installed software packages, excluding Nginx, and apply operating system patches. As the root user: (user: root)  
    `yum update --exclude=nginx`  
    * If kernel update was applied, reboot server then wait  for Phantom services to load.  
    * If no kernel was updated, then SU back to the phantom user then restart Phantom services: (user: phantom)  
    `exit`  
    `sudo su - phantom`  
    `/opt/phantom/bin/start_phantom.sh`  
    
9. Download the Official Unprivileged Tarball file for your operating system from the Splunk Phantom community website Product Downloads page.  

     * **Issue 1**: [my.phantom.us](https://my.phantom.us) only have the latest version of Phantom to download.  If you need older versions in order to do the step/incremental upgrades, open a suppose case to request for the file(s) before proceeding to the next step.  
     * **Issue 2**: As of Feb 8, 2021, I have not gone past this step because I need a copy of an older release to complete the step upgrades to get to the latest version. I am currently having issues with my Splunk contract, so I cannot create new support cases. :(
     * The **lesson learned** is to download every new Phantom release, even if I do not plan to upgrade because that new release may be the require version once I’m ready to upgrade to the latest version.  

10. Install the Splunk Phantom repositories and signing keys: (user: phantom)  
    `sudo su - phantom`  
    `cp phantom-<version>.tgz /opt/phantom/`  
    `tar -xvzf phantom-<version>.tgz`  

# Step 4 - Upgrade Phantom  
1. Reference: [Upgrade Unprivileged/tarball Phantom Instance](https://docs.splunk.com/Documentation/Phantom/4.9/Install/UpgradePhantomInstanceUnprivileged)  

2. Stop all Phantom services (user: phantom)  
    `/opt/phantom/bin/stop_phantom.sh`  

3. Disable WAL archiving for the PostgreSQL database (user: phantom)  
    `sed -i -e 's/archive_mode = on/archive_mode = off/i' /opt/phantom/data/db/postgresql.phantom.conf`  

4. Start Phantom services (user: phantom)  
    `/opt/phantom/bin/start_phantom.sh`  

5. Verify proxy server configuration for the phantom user. (user: root)
 * You must be able to successfully access any web site using wget and curl  
 * If not then switch to ROOT and verify that the required `export` commands are in place.  Otherwise, run the appropriate command(s) as follows:  

    > echo "export http_proxy=http://proxy.example.com:8080/" >> /etc/profile  
    > echo "export https_proxy=http://proxy.example.com:8080/" >> /etc/profile  
    > echo "export HTTP_PROXY=http://proxy.example.com:8080/" >> /etc/profile  
    > echo "export HTTPS_PROXY=http://proxy.example.com:8080/" >> /etc/profile  
    > echo "export http_proxy=http://proxy.example.com:8080/" >> /etc/profile.d/http_proxy.sh  
    > echo "export https_proxy=http://proxy.example.com:8080/" >> /etc/profile.d/http_proxy.sh  
    > echo "export http_proxy=http://proxy.example.com:8080/" >> /etc/profile.d/http_proxy.csh  
    > echo "export https_proxy=http://proxy.example.com:8080/" >> /etc/profile.d/http_proxy.csh  

6. Exit root, then switch to phantom user  
    `exit`  
    `sudo su - phantom`  

7. Run the upgrade script  
    `/opt/phantom/bin/phenv /opt/phantom/phantom_tar_install.sh upgrade --without-apps`  

    * Upgrade takes a **LONG TIME**  
    * **DO NOT WALK AWAY OR LEAVE TERMINAL WINDOW UNATTENDED**  
    * **PERIODICALLY PRESS ENTER TO ENSURE YOUR SSH SESSION STAYS ALIVE WHILE UPGRADING**  
    * To monitor progress:  
     Open a new terminal window.  Log in then switch to the phantom user  
     Run following command:  
	    `tail -f /opt/phantom/var/log/phantom/phantom_install_log`  


8. If the upgrade script produced the following message:  

    > To improve database performance, after completing the upgrade, run: /<PHANTOM_HOME>/bin/phenv /<PHANTOM_HOME>/usr/postgresql/bin/vacuumdb -h /tmp --all --analyze-in-stages  

     Then run the command:  

    `/opt/phantom/bin/phenv /opt/phantom/usr/postgresql/bin/vacuumdb -h /tmp --all --analyze-in-stages`  


9. After upgrading phantom you must run `phenv python3 /opt/phantom/bin/ibackup.pyc --setup` to continue using backup and restore functionality. Your current backups will be archived in the /opt/phantom/data directory.  

10. After the upgrade is complete, from **Main Menu** > **Administration** > **Administration Settings** > **Search Settings**  
    a. Select Playbooks from the drop-down menu.  
    b. Then click the **Reindex Search Data** button.  
