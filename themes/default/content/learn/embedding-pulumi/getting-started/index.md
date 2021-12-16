---
title: "Getting Started"
layout: topic
date: 2021-12-15
draft: false
description: |
    Set up your local system to start building a working example of the
    Automation API.
meta_desc: |
    Set up your local system to start building a working example of the
    Automation API.
index: 0
estimated_time: 5
meta_image: meta.png
authors:
    - laura-santamaria
tags:
    - learn
block_external_search_index: false
---

With all of that in mind, let's imagine that the Pulumipus Boba Tea Shop has
been going so well that we can expand to jump into other markets. We're now part
of a cloud engineering platform team, and we need to make it easy for developers
building our applications to request the infrastructure that they need through
some sort of API, perhaps a website.

This is exactly where Pulumi's Automation API can come in handy. Let's get
started!

## Setting up

We're going to automate the use of Pulumi's CLI so we can use it in pipelines,
APIs, and web interfaces. For the Pulumi program we're using to test our
automation, we'll get to some sample code in a moment. If you'd like to use your
own Pulumi program, however, go for it!

Just like in all of our other pathways, we're going to create a new Pulumi
project to hold our code. You could keep working in whatever project that you
already have set up, but it will get a bit messy if you do. Create a new
directory and run `pulumi new` with the python template. If you need a refresher
on how to do so, head to [Pulumi
Fundamentals]({{< relref "/learn/pulumi-fundamentals" >}}).

Call the directory `learn-auto-api`, which we'll use as the name in the rest of
this pathway. If you name it something different, don't forget to change the
value in the code!

## Making a local example

For our application to test the eventual API, we're going to use the Random
provider. If you want to use this sample code, make a new directory in
`learn-auto-api` called `burner-program`, and initialize a new Pulumi program
with the following code in `__main__.py`:

```python
import pulumi
import pulumi_random as random

fake_string = random.RandomString("fake-string",
                                  length=24
                                  )
fake_id = random.RandomId("fake-id",
                          byte_length=1)
fake_uuid = random.RandomUuid("fake-uuid")

pulumi.export('string', fake_string.result)
pulumi.export('id', fake_id.b64_std)
pulumi.export('uuid', fake_uuid.result)
```

This code should give us a good testing base that doesn't rely on any cloud
provider so we can rule out networking issues should something go awry.

<br/>
<br/>

Now that we have our project initialized, let's add some automation API code!