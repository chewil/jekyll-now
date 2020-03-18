---
layout: post
title: Splunk ES Assets and Inventory, Getting Started
published: false
date: 2020-03-15 19:19:00
category: Splunk ES
tags: Splunk ES asset-inventory
---

## Background
In my professional life, I work as a member of the InfoSec SOC team.  In addition to threat hunting, and investigating, I'm also hold an administrator role for our Splunk Enterprise Security.  In other words, I have to make sure, at a low level, all data sources are normalized and conform to CIM, and that the asset and identity data is up to date and be as complete as possible.   

## Scope
The scope of this blog is to focus on how I created and maintained the ES Asset and Identity data for the SOC team.  In my opinion, Asset and Identity is a fundamental step to turn ES into a meaningful and important tool because the additional enrichment information gave us more searching, filtering and data correlating capabilities.  Example of the enriched information are job title, role, system type, subnet, location, etc.

Use case example:
- Assign category of "known_scanner" to vulnerability scanners to easily filter out their activities by a simple `src_category!="known_scanner"`. There is no need to maintain a separate lookup table of known vulnerability scanners.
- Set notable event priority based on a user's role, so `user_category=executive` would have a higher priority notable event created.

## Assets
In a nutshell, I consider any networked device on the internal or managed networks as an asset.  

Lookup tables
App: SA-IdentityManagement
asset_lookup_by_str - assets
asset_lookup_by_cidr - subnets
identity_lookup_expanded - identities

