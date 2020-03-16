---
layout: post
title: Splunk ES Assets and Inventory, Getting Started
published: false
date: 2020-03-15 19:19:00
category: Splunk ES
tags: Splunk ES asset-inventory
---

## Background
I have used and administered small scale Splunk deployments on and off for many years.    I knew my way around simple administrative tasks (inputs, field extractions, apps, etc) and a good grasp of Splunk transformation commands (eval, stats, chart, timechart, etc.)  It wasn’t until October 2017 when I had my first exposure to Splunk Enterprise Security when I joined the SOC Team as a Senior SOC Engineer.  I was tasked to work with Professional Services get ES up and running.

While this was going on, I still have my operational duties to split my time and attention.  So we ended up with all the data available in ES, but they were not tuned.  We had tens of thousands of notable events each day.  Asset and Identity Investigator dashboards were showing wrong and incorrect results.  ES, essentially, was not usable for the SOC team.  We ended up working out of the default "Search" app, and save all our alerts and report there.  We tried, but couldn't find ES useful at all.

## Scope
The scope of this blog is to focus on how I created and maintained ES Asset and Identity information.  In my opinion, Asset and Identity is a critical step to get ES going because the additional enrichment information gave us more searching, filtering and data correlating capabilities that.  The enrichment information could be job title, role, system type, subnet, location, etc.

For example:
- Assign category of "known_scanner" to our vulnerability scanners so we can easily filter out their activities by a simple `src_category!="known_scanner"`. There is no need to maintain a separate lookup table of known vulnerability scanners.
- Set notable event priority based on a user's role, so user_category=executive would have a higher priority.


