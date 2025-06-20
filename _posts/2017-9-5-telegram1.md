---
layout: post
title: Atlassian Bamboo's build's info + Telegram bot
date: 2017-09-05 14:00:01 +0300
categories: CI bamboo linux python
---

Hi! I want to share my experience of creating a Telegram bot, taht sends information about a given Atlassian Bamboo's plan to the chat/channel.

### Step 1: Get Telegram bot's token

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/tele1.png" alt="Bot father" title="Bot father" width="80%">
</div>

Easy as 1-2-3! Just find `BotFather` in the Telegram. Invoke `/Start` to start working with it. Follow the on-screen instructions. The BotFather will ask you just a couple of question and return your bot's information. Keep it secret!

### Step 2: Python code

```python
#!/usr/bin/env python3

from urllib.request import urlopen
from urllib import request, parse
import json
import time
import datetime

bambooplans = ("PROJ-PLAN1")
restapiurl = "https://bamboo-in-your-company/rest/api/latest/result/"
restapiopt =".json?max-results=1"
tg_url = 'https://api.telegram.org/botxxxxxxxxx:yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy/sendMessage?chat_id=zzzzzzzzz'

def notify():

	weekdayinfo1 = datetime.datetime.today().strftime("%A, ")
	weekdayinfo2 = time.strftime("%d.%m.%Y")

	data_to_be_sent = weekdayinfo1+weekdayinfo2+"\nAutomatic Report\n"

	data = parse.urlencode({'text': data_to_be_sent}).encode()
	req = request.Request(tg_url, data=data)
	resp = request.urlopen(req)

	for plan in sorted(bambooplans):

		def get_json_data(url):
			response = urlopen(url)
			data = response.read().decode("utf-8")
			return json.loads(data)

		url = (restapiurl+plan+restapiopt)
		jsonrestlist = (get_json_data(url))
		
		info1 = "Plan name: "+str(jsonrestlist["results"]["result"][0]["plan"]["name"])+"\n"
		info2 = "Last build's "+str(jsonrestlist["results"]["result"][0]["buildNumber"])+" status is "+str(jsonrestlist["results"]["result"][0]["buildState"])+"\n"

		data_to_be_sent = info1+info2

		data = parse.urlencode({'text': data_to_be_sent}).encode()
		req = request.Request(tg_url, data=data)
		resp = request.urlopen(req)

if __name__ == "__main__":
	notify()
```

As you see, you can easily tune this script for your purposes. Just change the following parts:

* `bamboo-in-your-company` - your Bamboo instance's basic URL;
* `xxxxxxxxx:yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy` - Telegram bot's token;
* `zzzzzzzzz` - chat id. If you want to receive personal messages, just insert here your TG user_id;
* `PROJ-PLAN1` - bamboo plan id. `bambooplans` is a Python tuple, so you can add as many plans as you need, separated with `,`.  

As a result, you could the report similar to this:

<div style="text-align: center; background-color: #eee; padding: 10px;">
  <img src="{{ site.baseurl }}/images/tele2.png" alt="Bot" title="Bot" width="80%">
</div>

Pretty easy, right? Now you can add this script to any sceduler you use and always stay informed about your important builds! 

If you have any question - you're welcome to ask in a comment section!

See you!
