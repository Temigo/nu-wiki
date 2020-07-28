---
title: Computing Accounts
description: 
published: true
date: 2020-07-28T20:56:01.058Z
tags: 
---

## Step 1: Getting your account

There are two types of accounts: **unix** and **windows**. 

**Unix** account is used to use computing resources at SLAC. It's the account you would use to ssh into computing nodes, submit grid jobs, etc. Usual stuffs!

**Windows** account is used for emails, scheduler apps, HR websites, travel request/report, IT service ticket, etc.. If you are a SLAC employee, you need this. If you are a non-SLAC collaborator, you might not need this.

### How to request an account?
* **Step 1.1**: request SLAC user account (called SLUO). I put [instructions here](/guides/sluo-howto). This may take some time (a few days to a week), so submit asap!
* **Step 1.2**: once the request is approved, you should receive an email notifying that and your "system ID". Contact [Kazu](mailto:kterao@slac.stanford.edu) with following information:
  * Your system ID
  * Name of your home institution
  * **TWO** preferred unix user account name to be created for you, in the order of preference. Your account name may contain digits 0-9, lower-case letters a-z, and the special character "-". Must begin with a lower-case letter a-z. Must not be shorter than 3 or longer than 8 characters.

### To-do's after getting an account
* Make sure you complete the cyber security training, or your account will be disabled soon.
* Go to [this page](https://www.slac.stanford.edu/comp/unix/auth/afs-self.shtml) and set your unix `$HOME` directory space quota to 20GB

### Configuring Email app
Here is the [link](http://www2.slac.stanford.edu/comp/messaging/Installing/default.htm) to see how to configure your SLAC email on apple Mail, Thunderbird, etc..

### SLAC VPN
VPN is needed when you want to use (some) printers and access management tools like peoplesoft. Most of you may not need these services, so don't bother unless you need them.
* Submit a VPN access request from [IT service desk](https://slacprod.servicenowservices.com/it_services)
* Download Cisco AnyConnect Secure Mobility Client, connect to su-vpn.stanford.edu
