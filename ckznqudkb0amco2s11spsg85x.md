---
title: "Appwrite: set up email sending under 10 minutes"
seoTitle: "Appwrite: set up email sending under 10 minutes"
seoDescription: "Every app needs email sending capabilites weather it's for password reset or invitations or whatever - appwrite has everything prepared for it"
datePublished: Tue Feb 15 2022 06:27:52 GMT+0000 (Coordinated Universal Time)
cuid: ckznqudkb0amco2s11spsg85x
slug: appwrite-set-up-email-sending-under-10-minutes
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1644784806538/ffm8C82km.png
tags: app-development, opensource, email, baas, 2articles1week

---

Every app needs email sending capabilites weather it's for password reset or invitations or whatever - appwrite has everything prepared for it, we just need to use the given options. 

Because SMTP servers are hard to setup and maintain, we'll use a third party API to provide our SMTP needs.

## Mailgun side settings

Since [mailgun](https://www.mailgun.com/) is super easy to setup and use, we'll use it as our SMTP provider. 

After a successful registration:
1. Navigato to sending -> overview
2. Select SMTP
3. Those are the credits that are needed for us in the later setup

E.g.:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1644869352960/Y1cruBytW.png)

I already reset this password, so no worries about that 😊

## Appwrite setting

After grabbing our settings we need to setup for appwrite:

1. Navigate to your installation forlder for me it's in `~/appwrite`
2. Start to edit your `.env` in the folder
3. After you've edited the file restart the appwrite compose

You'll need to edit: `_APP_SMTP_HOST`, `_APP_SMTP_PORT`, `_APP_SMTP_SECURE`, `_APP_SMTP_USERNAME` and `_APP_SMTP_PASSWORD`. 

Optionally you can also edit: `_APP_SYSTEM_EMAIL_ADDRESS` - this is the sender's email, for this short example I left as is. 

E.g.:
![env (1).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1644870038701/UmDqx7Mqv.png)

To restart appwrite you first need to stop the containers:

```bash
$ docker-compose stop
```

Then after the containers are stopped - restart it:

```bash
$ docker-compose up -d
```

These commands should be executed from your appwrite installation folder, for me: `~/appwrite`. 

Aaand that's it, you've successfully setup email sending for your appwrite. To try out go to your console, select a project, settings and invite a new member for it. After you've sent the email mailgun should show something like this:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1644870530697/Ui2YD2ujg.png)

## Closing thoughts

You can also read the full instructions about email sending in [appwrite docs](https://appwrite.io/docs/email-delivery). 

I hope you've found this quick article about email setup useful. 😊

## Thank you for reading, and let's connect!

Thank you for reading my post! Feel free to subscribe to my email newsletter and connect on [LinkedIn](https://www.linkedin.com/in/bence-hornyak/) or [Twitter](https://twitter.com/b_hornyak) 😊