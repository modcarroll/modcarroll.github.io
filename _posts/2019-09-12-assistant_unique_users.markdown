---
layout: post
title:  "IBM Watson Assistant: How Do I Determine Unique Users?"
date:   2019-09-12 16:00:00
categories:
comments: true
tags: 
# image: /assets/article_images/2014-11-30-mediator_features/night-track.JPG
# image2: /assets/article_images/2014-11-30-mediator_features/night-track-mobile.JPG
---
IBM Watson Assistant: How Do I Determine Unique Users?

Originally posted on [Medium](https://medium.com/@modlanglais/ibm-watson-assistant-how-do-i-determine-unique-users-3aef8d3a2b8e)

If you are familiar with the Watson Assistant Plus plan, then you have likely come across the same question as others: How do I determine what constitutes a "unique" user?

The Watson Assistant Plus plan comes with several super cool features, including disambiguation, intent recommendations, and user-based pricing. You can read more from the official announcement: [https://www.ibm.com/blogs/watson/2018/12/introducing-watson-assistant-plus-pricing-plan-and-new-powerful-features/](https://www.ibm.com/blogs/watson/2018/12/introducing-watson-assistant-plus-pricing-plan-and-new-powerful-features/)

The Plus (and Premium) plan bases its pricing on MAUs (Monthly Active Users). Consider this scenario: You are the owner of a floral shop specializing in yellow roses (my favorite). You would likely create a chatbot to answer common customer inquiries. These may include: what are your store hours, what types of flowers do you sell, and it can look up a customer's order information. For previous Watson Assistant pricing plans, you were charged per Message API call. If a customer asks, "What type of flowers do you specialize in?" and the bot responds with "We specialize in all types of yellow roses." then that is 1 Message API call. Each subsequent utterance is another Message API call.
With the Plus plan, you are charged a base rate for "x" number of users (tiered, starting at 10,000 users). So each user could talk to the bot 100 times per week if they wanted, but they are still only considered to be one user. Now to the original point, how do I tell Watson, who is a unique user? In your API calls, there is a parameter called `user_id` which you use to send Watson Assistant the ID for your user. [https://cloud.ibm.com/apidocs/assistant-v2#send-user-input-to-assistant](https://cloud.ibm.com/apidocs/assistant-v2#send-user-input-to-assistant)

![Watson Assistant API Documentation](/assets/article_images/user_id.png)

You should give each of your users a unique identifier (in the form of a string) as their user ID. Many companies already implement some sort of authentication for their customers, so you may choose to use their existing e-mail address or user ID from your website as their user ID. So if I log in to my floral website with the e-mail address morganlanglais@ibm.com, then when I chat with the chatbot while logged in, my chatbot orchestration layer could grab my e-mail address, add it to the payload as the "user_id" then send my message to Watson Assistant. Here's a snippet to demonstrate:

```javascript
// assume currentUser is the current logged in user
let currentUser = {
  email: "morganlanglais@ibm.com",
  name: "Morgan Langlais",
  city: "Austin"
}

// get e-mail address from currentUser object
let wa_user_id = currentUser.email;

// add cookie ID to user_id parameter
const AssistantV2 = require('ibm-watson/assistant/v2');

const assistant_service = new AssistantV2({
  iam_apikey: '{apikey}',
  version: '2019-02-28',
  url: '{url}'
});

assistant_service.message({
  assistant_id: '{assistant_id}',
  session_id: '{session_id}',
  input: {
    'message_type': 'text',
    'text': 'Hello'
    },
  context: {
    'global': {
      'system': {
        'user_id': wa_user_id
      }
    }
  }
  })
  .then(res => {
    console.log(JSON.stringify(res, null, 2));
  })
  .catch(err => {
    console.log(err);
  });
  ```

There is a problem here, however. What if you have a public-facing chatbot? My bot is on the homepage of my floral website. I want any user whether they are currently logged in or not to be able to converse with my chatbot. After all, how else will they know that I specialize in yellow roses? However, if they aren't logged in, how can I assign a unique "user_id" to them? If I don't, each API call is considered a unique user. (Update: This applies to v1. v2 uses sessions instead of API calls. So in v2, each session/conversation would be considered a unique user.) The answer: cookies 🍪! When the user visits your website, a cookie is saved on their computer. Then, your website recognizes this cookie on each subsequent visit. You can determine a unique ID for each user to save in the cookie then retrieve that and pass it to Watson Assistant as the "user_id" parameter. Here's a simple example:

```javascript
// set cookie. For this user, their cookie ID is 'purplecookie1' but each user should have a unique cookie ID.
document.cookie = "cookie_id=purplecookie1";

// get cookie
let cookie_string = document.cookie.split("=");
let wa_user_id = cookie_string[0];

// add cookie ID to user_id parameter
const AssistantV2 = require('ibm-watson/assistant/v2');

const assistant_service = new AssistantV2({
  iam_apikey: '{apikey}',
  version: '2019-02-28',
  url: '{url}'
});

assistant_service.message({
  assistant_id: '{assistant_id}',
  session_id: '{session_id}',
  input: {
    'message_type': 'text',
    'text': 'Hello'
    },
  context: {
    'global': {
      'system': {
        'user_id': wa_user_id
      }
    }
  }
  })
  .then(res => {
    console.log(JSON.stringify(res, null, 2));
  })
  .catch(err => {
    console.log(err);
  });
  ```

If you are uninterested in using an authentication layer or cookies, you may try using a user's IP address. There are a few problems with this approach (dynamic IP addresses and VPNs come to mind), but it may work as a rudimentary option.

```javascript
// get user's IP address using third-party service
userIP = '';
$.getJSON('https://json.geoiplookup.io/api?callback=?', function(data) {
  console.log(JSON.stringify(data, null, 2));
  userIP = data.ip;
});

let wa_user_id = userIP;

// add cookie ID to user_id parameter
const AssistantV2 = require('ibm-watson/assistant/v2');

const assistant_service = new AssistantV2({
  iam_apikey: '{apikey}',
  version: '2019-02-28',
  url: '{url}'
});

assistant_service.message({
  assistant_id: '{assistant_id}',
  session_id: '{session_id}',
  input: {
    'message_type': 'text',
    'text': 'Hello'
    },
  context: {
    'global': {
      'system': {
        'user_id': wa_user_id
      }
    }
  }
  })
  .then(res => {
    console.log(JSON.stringify(res, null, 2));
  })
  .catch(err => {
    console.log(err);
  });
  ```

Note: These code snippets are examples ONLY and are not intended to be implemented in a production environment.