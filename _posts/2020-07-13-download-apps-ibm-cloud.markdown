---
layout: post
title:  "How to Download Application Code from IBM Cloud"
date:   2020-07-13 14:05:07
categories: feature
tags:
comments: true
image: /assets/article_images/fatal_error.png
---
FATAL ERROR: Where is my code??

A while back I came across a [Node package](https://www.npmjs.com/package/watson-assistant-skills-merger) which allows you to merge two Watson Assistant skills. This is useful if you have multiple users working on the same skill separately and you need to merge the changes. Think GitHub diff meets Watson Assistant.

To use this package, you need an entire Node app, and most users do not want to spin up an entire app to just merge two skills. So, I put one together for anyone to use: [http://wa-merge-tool.mybluemix.net/](http://wa-merge-tool.mybluemix.net/)

I uploaded it to IBM Cloud and forgot all about it. I knew that it would run without a hitch, and it has! There was just one problem: I forgot to commit my code before I deleted it from my desktop. I'm a bit of a neat-freak when it comes to my desktop, so I don't keep any files locally longer than I have to. I usually upload to GitHub then delete my local copy. I had completely forgotten to do that this time!

Luckily, after some research, I found a way to retrieve my uploaded app from IBM Cloud. My code was not lost after all. Insert a sigh of relief here. If you find yourself in a similar predicament, just follow these steps:

For the sake of this tutorial, assume my application name is "morgans-awesome-app"

**Tip:** It's a good idea to run the terminal from your desktop before executing these steps so it will be easy to locate your application files later.

### Step 1
Download the [Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

### Step 2
Login using the CF CLI:
Regular: `cf login`
SSO: `cf login --sso`
Upon logging in, the CLI will prompt you to select the org in which your application lives. Type in the corresponding number and press enter.

### Step 3
Optionally, list your applications:
`cf apps`

### Step 4
First you will need to SSH into your application and create a zip file to download. Make sure to enter the application name exactly as it was listed in the previous step. `cf ssh` will log you into the app:
`cf ssh morgans-awesome-app`

### Step 5
Your application files are saved in the `app` folder. Let's create a .zip file containing all of the code:
`zip -r app.zip app`

### Step 6
Verify that the .zip file was created. Type `ls` and press enter to see a list of all files in the current directory. Here is an example where I can see that my .zip file has been created (app GUID redacted for privacy):
[![](/assets/article_images/ls-example.png)](/assets/article_images/ls-example.png)

### Step 7
Exit the SSH session:
`exit`

### Step 8
Retrieve the GUID for your application. Make sure to copy and paste this somewhere for later:
`cf app morgans-awesome-app --guid`

### Step 9
Retrieve connection information for your app:
`cf curl /v2/info`

### Step 10
Get the SSH password:
`cf ssh-code`

### Step 11
To format the command which will actually copy your code from IBM Cloud to your Desktop, follow this structure:
`scp -P [PORT-NUMBER] -o User=cf:[GUID]/[INSTANCE-ID] [APP-SSH-ENDPOINT]:[REMOTE-DIRECTORY-TO-FILE] [LOCAL-FILE-DESTINATION]`

**PORT-NUMBER:** Port number for your application.

**GUID:** Your applications' GUID.

**INSTANCE-ID:** The instance ID of your application.

**APP-SSH-ENDPOINT:** IBM Cloud application endpoint.

**REMOTE-DIRECTORY-TO-FILE:** Directory for the file you would like to download.

**LOCAL-FILE-DESTINATION:** Where to save the file locally.

### Detailed directions for formatting the command:

When you ran the `cf curl /v2/info` command, you received a list of data. Find the "app_ssh_endpoint" field. For example, mine is "ssh.us-south.cf.cloud.ibm.com:2222". This tells me that my endpoint is "ssh.us-south.cf.cloud.ibm.com" and the port number is "2222". So far for our command we have:

`scp -P 2222 -o User=cf:[GUID]/[INSTANCE-ID] ssh.us-south.cf.cloud.ibm.com:[REMOTE-DIRECTORY-TO-FILE] [LOCAL-FILE-DESTINATION]`

We retrieved the GUID during step 8 above, and the instance ID is typically 0. If you have multiple instances of the app running, then select the instance number that you would like to download. If you are unsure, use 0. Our command is coming together:

`scp -P 2222 -o User=cf:some-guid-here/0 ssh.us-south.cf.cloud.ibm.com:[REMOTE-DIRECTORY-TO-FILE] [LOCAL-FILE-DESTINATION]`

Earlier we created a .zip file called `app.zip` in the root directory of our application container, so the remote directory to our file is simply `app.zip`. Almost there:

`scp -P 2222 -o User=cf:some-guid-here/0 ssh.us-south.cf.cloud.ibm.com:app.zip [LOCAL-FILE-DESTINATION]`

The local file destination is the location where you would like to save the .zip file on your local machine. I would like to save the .zip file to my Desktop, so I will use `~/Desktop/app.zip` Please note that this will be different for Windows.

My final command will look like this:
`scp -P 2222 -o User=cf:some-guid-here/0 ssh.us-south.cf.cloud.ibm.com:app.zip ~/Desktop/app.zip`

The `scp` command stands for "secure copy." This will copy my .zip file from my IBM Cloud account to my desktop. If you are prompted to enter a password, enter the result from `cf ssh-code` that you executed in step 10.

## Happy coding!
