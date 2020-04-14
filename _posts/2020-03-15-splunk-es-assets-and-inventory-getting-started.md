---
layout: post
title: Splunk ES Assets and Inventory, Getting Started
published: true
date: 2020-03-15 19:19:00
category: Splunk ES
tags: Splunk ES asset-inventory
---

## Obligatory Disclaimer
Opinions expressed are solely my own and do not express the views or opinions of my employer or of Splunk...  

======
The following are my notes as I was setting up ES’s asset and identity information.  The methods I used may not be applicable to all environments, but hopefully they will provide some tips on how to adapt them in other environments.

One of the immediate benefit of having a populated ES asset and identity table is the enrichment fields showing up for the src, dest, doc and user fields.  Example of the additional enrichment fields are:  _category, _ip, _nt_host, _priority, user’s full name, user’s manager, etc.

These enrichment fields comes from the 3 lookup tables in the SA-IdentityManagement app.  That app also have 9 automatic lookups defined to output the enriched fields.

**The 3 lookup tables are:**

- `asset_lookup_by_str` - A list of assets.  This should be a list of all known networking capable hardware.  This include all company owned, vendor deployed and user BYOD devices that could be connected to your managed networks or VPN. 
	- Note:  This list is kept up to date and completed by merging asset data from multiple sources.  The method used for the merging is custom and I will discuss in more details later.
- `asset_lookup_by_cidr` - A subset of the `asset_lookup_by_str` lookup table containing only the rows where the `ip` field is in CIDR format that are 31 or less bits. I personally do not find this lookup table very useful, and have made some modifications that I will mention later.
- `identity_lookup_expanded` - A list of identities, or user accounts from AD.

Try viewing these 3 lookup tables using the `| inputlookup` command to see what information are contained within each.  Splunk has a really good document on this topic on [Splunk Docs](https://docs.splunk.com/Documentation/ES/latest/Admin/Addassetandidentitydata).

In that doc, Splunk listed many data sources to gather asset and identity information from.  Those are great for starting up, however, it’s best to look at all source data collected in Splunk and look for any of them that contains combinations of hostname, FQDN, IP, MAC and user information.  

**Issue:**  TA’s from SplunkBase, even the ones mentioned in the Doc, rarely have all the fields extracted or named correctly.  So expect to spend a LONG time tweaking the field extraction, alias, calculation and transformation rules.  

**Issue:**  There are saved searches in SA-IdentityManagement that will merge all the asset and identity sources into those 3 lookup tables above.  They do not, however, dedup the information, so only the first entry found will be returned.   I will discuss how I modify these saved searches to properly merge and dedup the asset and identity data.



