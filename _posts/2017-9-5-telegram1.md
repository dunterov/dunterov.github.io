---
layout: post
title: Telegram bot + bamboo build's status
date: 2017- 14:00:01 +0300
categories: CI bamboo linux python
---

Hi! I'll share you my experience of creating a Telegram bot, which sends information about a given Atlassian Bamboo's plan. I'll use python 3 for this task.

### Step 1: get TG's bot token

![Bot father]({{ site.baseurl }}/images/tele1.png "Bot father")

Easy as 1-2-3. Just find BotFather in the telegram. `/Start` work with it. Follow the on-screen instructions. The BotFather will ask you just a couple of question and return your bot's information. Keep it secret!

### Step 2: python 3 code

```python
#!/usr/bin/env python3

from urllib.request import urlopen
from urllib import request, parse
import json
import time
import datetime

#Bunch of variables
bambooplans = ("PROJ-PLAN1")
restapiurl = "https://bamboo-in-your-company/rest/api/latest/result/"
restapiopt =".json?max-results=1"
tg_url = 'https://api.telegram.org/botxxxxxxxxx:yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy/sendMessage?chat_id=zzzzzzzzz'

#main magic inside
def ordinar_work():

#which day of week is it?
	weekno = datetime.datetime.today().weekday()

	if weekno == 0:
		weekdayinfo = "Monday, "
	elif weekno == 1:
		weekdayinfo = "Tuesday, "
	elif weekno == 2:
		weekdayinfo = "Wednesday, "
	elif weekno == 3:
		weekdayinfo = "Thursday, "
	elif weekno == 4:
		weekdayinfo = "Friday, "
	elif weekno == 5:
		weekdayinfo = "Saturday, "
	elif weekno == 6:
		weekdayinfo = "Sunday, "

	weekdayinfo1 = weekdayinfo
	weekdayinfo2 = time.strftime("%d.%m.%Y")

#info welcome message
	data_to_be_sent = weekdayinfo1+weekdayinfo2+"\nAutomatic Report\n"

	data = parse.urlencode({'text': data_to_be_sent}).encode()
	req =  request.Request(tg_url, data=data)
	resp = request.urlopen(req)

#loop for plans from bambooplans list
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
		req =  request.Request(tg_url, data=data)
		resp = request.urlopen(req)

if __name__ == "__main__":
	ordinar_work()
else:
	False
```

As you see, you can easily tune this script for your purposes. Just change the following parts:

`bamboo-in-your-company` - your Bamboo instance's basic URL;
`xxxxxxxxx:yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy` - TG bot's token;
`zzzzzzzzz` - chat id. If you want to receive personal messages, just insert here your TG user_id;
`PROJ-PLAN1` - bamboo plan id. `bambooplans` is a Python tuple, so you can add as many plans as you need, separated with ',' (comma).  

So, example of report is like that:

![Bot]({{ site.baseurl }}/images/tele2.png "Bot")

Pretty easy, right? Now you can add this script to any sceduler you use and always stay informed about your important builds! 

If you have any question you welcome to ask at comment section!

See you!