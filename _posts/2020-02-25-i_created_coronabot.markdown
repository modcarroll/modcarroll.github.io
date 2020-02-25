---
layout: post
title:  "Novel CoronaBot"
date:   2020-02-25 14:42:11
categories: feature
tags: 
image: /assets/article_images/coronavirus-cdc-banner.jpg
---
Yes, another bot.

Similar to Rule 34 of the internet, there seems to be a chatbot for everything. If there isn't, then it follows by Rule 35 that there will likely be one coming soon. So naturally with the recent Coronavirus outbreak, the bots have been popping up. As a self-avowed chatbot expert, I naturally felt the need to jump on the bandwagon. The difference is that my bot is built with [WATSON](https://www.ibm.com/watson).

There are a couple of bots out there now that are employing plenty of scare tactics: casualty statistics, frequent pushed updates, excessive emoji usage... but in an effort to build a friendlier, non-panic-enducing bot, I decided to simply pass on the data from the CDC. So here is how it works: Watson Assistant connects to Watson Discovery which then crawls the CDC's Coronavirus website. When you submit a question to the chatbot, Watson Assistant will pass the query to Discovery, parse the web crawl results, then return a few relevant links to you.

That's it! Think of it as a simple, non-biased way to search the CDC's website for the latest information. The crawler auto-updates every hour, so you can expect relevant results every time. To get started, click the green box at the bottom right of this page.

To learn how to build your own bot: [https://cloud.ibm.com/docs/assistant?topic=assistant-getting-started](https://cloud.ibm.com/docs/assistant?topic=assistant-getting-started)
