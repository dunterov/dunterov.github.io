---
layout: post
title: How to start working with OpenAI in Python
date: 2025-05-06 10:00:01 +0100
categories: llm openai python
---

Alright, we can't avoid it anymore! LLMs are everywhere, and Python is the way I usually talk to them. Whether you're building tools, bots, or just experimenting, that's becoming impossible to ignore. In this short post I'll show how to start working with OpenAI and Python.

### Register and obtain API token

1. Go to https://platform.openai.com
2. Sign in (or create an account)
3. Open [API Keys](https://platform.openai.com/api-keys) page
4. Click "+ Create new secret key"
5. Provide name and project and click "Create secret key"
6. Save it somewhere, we will use it later

That's it, we are ready to use the OpenAI API with Python now.

### Python part

First, we need to install the library:

```bash
pip install openai
```

Now, let's create a simple file, let's say `try_openai.py` with the following content:

```python
import os
from openai import OpenAI

api_key = os.getenv("OPENAI_API_KEY")
client = OpenAI(api_key=api_key)

prompt = "what does the fox say?"

response = client.responses.create(
        model="gpt-4.1-mini",
        input=prompt
        )

print(f"You have asked: {prompt}")
print("LLM's answer is:")
print(response.output_text)
```

You won't believe me, but that's it! Now let's run it and see what'll happen:

```bash
export OPENAI_API_KEY='<YOUR API KEY HERE>'
python ./try_openai.py
```

The output will be similart to this (depends on the `model` we used for this. In our example it is `gpt-4.1-mini`):

```
You have asked: what does the fox say?
LLM's answer is:
"The fox says" is a reference to the viral song "The Fox (What Does the Fox Say?)" by the Norwegian comedy duo Ylvis. In the song, they humorously speculate about the sounds a fox might make, suggesting various funny and nonsensical noises like:

- "Ring-ding-ding-ding-dingeringeding!"
- "Wa-pa-pa-pa-pa-pa-pow!"
- "Hatee-hatee-hatee-ho!"
- "Joff-tchoff-tchoffo-tchoffo-tchoff!"

In reality, foxes make a variety of sounds including barks, screams, howls, and gekkering (a guttural chattering noise), used for communication.
```

### What's next?

Now, knowing this you can start building your own apps and services powered by AI! Also, for some of you it might be interesting to check this small example project I recently published - [oa2tg](https://github.com/dunterov/oa2tg). It's a small experimental tool to post AI generated content to the Telegram channel.

See ya!
