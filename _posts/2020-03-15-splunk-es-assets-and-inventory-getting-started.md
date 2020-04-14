---
layout: post
title: Splunk ES Assets and Inventory, Getting Started
published: false
date: 2020-03-15 19:19:00
category: Splunk ES
tags: Splunk ES asset-inventory
---

## Obligatory Disclaimer
Opinions expressed are solely my own and do not express the views or opinions of my employer or of Splunk...  In addition, the example and use cases provided may not be applicable nor suitable for all environments. 

## Scope
Splunk Enterprise Security requires that it has access to accurate and up to date asset and identity inforamtion for data enrichment.  Therefore, the scope is to bring out the methods and processes I'm using to maintain asset and identity inforamtion for my ES implementation.
Splunk Enterprise Security requires that it has access to accurate and up to date asset and identity inforamtion for data enrichment.  Therefore, the scope is to bring out the methods and processes I'm using to maintain asset and identity inforamtion for my ES implementation.

### Use case example
- Assign category of "known_scanner" to vulnerability scanners to easily filter out their activities by a simple `src_category!="known_scanner"`. With this defined, there is no need to maintain a separate lookup table.
- Notable events created with higher urgency level based on the priority of the user or asset.  So C2 traffic coming from a PCI or other critical priority assets will have a higher urgency value notable event created than, for example, an unmanaged computer from the guest wifi.

<<<<<<< HEAD
## Useful Lookup Tables
ES asset and identity are just a bunch of lookup tables.  There are automatic lookups that uses these lookup tables to enrich the src, dest, dvc and user fields.  There are also many saved searches that updates these lookup tables. The following are 3 lookup tables that I commonly reference that are globally shared and located within the context of the `SA-IdentityManagement` app that came bundled with ES.

- `asset_lookup_by_str` - A list of assets.  This should be a list of all known networking capable hardware.  This include all company owned, vendor deployed and user BYOD devices that could be connected to your managed networks or VPN. 
	- Note:  This list is kept up to date and completed by merging asset data from multiple sources.  The method used for the merging is custom and I will discuss in more details later.
- `asset_lookup_by_cidr` - A subset of the `asset_lookup_by_str` lookup table containing only the rows where the `ip` field is in CIDR format that are 31 or less bits. I personally do not find this lookup table very useful, and have made some modifications that I will mention later.
- `identity_lookup_expanded` - A list of identities, or user accounts from AD.

## Useful Macros
tesr
=======
## Assets
Consider any networked device on the internal or managed networks as an asset.  
>>>>>>> 5c441a73563eb83c742d6ebe92cf2cef670245c2


