---
layout: post
title: Splunk ES Assets and Inventory, Getting Started
published: true
date: 2020-03-15 19:19:00
category: Splunk ES
tags: Splunk ES asset-inventory
---
*Opinions expressed are solely my own and do not express the views or opinions of my employer, of Splunk, or anyone else...*  

The following are from the various notes I took as I was setting up ES’s asset and identity information at work.  These methods may not be applicable to all environments, so use them as references in your environments.  As of this writing (August, 2020) I am using Splunk Enterprise 7.1.x with ES 5.02.  The newer version of ES handles assets and inventory more efficiently, but, IMO, the methods and process should still be relevant.

# Benefits of ES Assets and Identity
An immediate benefit after populating the ES asset and identity tables is the enrichment fields showing up for the `src`, `dest`, `dvc` and `user` fields.  Example of the additional enrichment fields are:  category, ip, hostname, priority, user’s full name, user’s manager, etc.

# Lookup Tables

1- `asset_lookup_by_str` - A list of assets.  This should be a list of all known networking capable hardware.  This include all company owned, vendor deployed and user BYOD devices that could be connected to your managed networks or VPN.
	- Note:  This list is kept up to date and completed by merging asset data from multiple sources.  The method used for the merging is custom and I will discuss in more details later.

2- `asset_lookup_by_cidr` - A subset of the `asset_lookup_by_str` lookup table containing only the rows where the `ip` field is in CIDR format that are 31 or less bits. I personally do not find this lookup table very useful, and have made some modifications that I will mention later.

3- `identity_lookup_expanded` - A list of identities, or user accounts from AD.

Try viewing these 3 lookup tables using the `| inputlookup` command to see what information are contained within each.  Splunk has a really good document on this topic on Splunk Docs on the topic of ["adding asset and identity data to Splunk ES"](https://docs.splunk.com/Documentation/ES/latest/Admin/Addassetandidentitydata).

In that doc, Splunk listed many data sources to gather asset and identity information from.  Those are great for starting up, however, it’s best to look at all source data collected in Splunk and look for any of them that contains combinations of hostname, FQDN, IP, MAC and user information.  

**Issue:**  TA’s from SplunkBase, even the ones mentioned in the Doc, rarely have all the fields extracted or named correctly.

- Expect to spend a LONG time tweaking the field extraction, alias, calculation and transformation rules.  

**Issue:**  There are saved searches in SA-IdentityManagement that will merge all the asset and identity sources into those 3 lookup tables above.  They do not, however, dedup the information, so only the first entry found will be returned by the lookup.  

- Modify ES's merge scripts to properly merge the relevant data and remove duplicates.

## Save Searches

Test
