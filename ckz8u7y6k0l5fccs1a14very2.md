---
title: "Appwrite: error logging with sentry"
seoTitle: "Appwrite: error logging with sentry"
seoDescription: "With the recent appwrite version 0.12.0 it is now possible to integrate external logging providers into appwrite"
datePublished: Fri Feb 04 2022 20:05:52 GMT+0000 (Coordinated Universal Time)
cuid: ckz8u7y6k0l5fccs1a14very2
slug: appwrite-error-logging-with-sentry
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1643641386164/TiQt8e7Id.png
tags: opensource, php, error-handling, webdev, 2articles1week

---

With the recent appwrite version `0.12.0` it is now possible to integrate external logging providers into appwrite. With that you can catch unhandled exceptions and fix them or report them to the appwrite team. 

## Importance of using error trackers 

As software engineers we try to think about the unexpected and try to prepare our software to handle every sort of input, but there are edge cases and sometimes the unexpected can happen. That's why you need to capture those events and respond to them. 

Some benefits using error trackers:
- Faster response time to your issues - this means happier customers which is always a good thing
- Learn - you can always iterate your software, learn from your mistakes, so your future self doesn't make the same mistake

So why [Sentry](https://sentry.io/)?

Sentry has:
- an easy to use issues UI - you can query them, assign team members to take care of them and many more feature is provided
- a great integrations with your favourite git servers (gitlab, github, bitbucket) and many more integration options which eases your life
- a self hosted option 

Sentry has a lot more to offer - make sure to check out the [docs](https://docs.sentry.io/product/)!

## Integrate sentry into appwrite 

To integrate sentry into appwrite you need to find your appwrite folder, it is located in the folder where you issued the install command. After you located that cd into the folder and change `_APP_LOGGING_PROVIDER` and `_APP_LOGGING_CONFIG` in the `.env` file like in the example: 

![code_sentry_config.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643971958828/mqGnHcIYe.png)

The config also supports self hosted sentry then you would have to define the url for your installation as the third part separated with a `;` character. 

The provider is self explanatory I think 😀 

The `_APP_LOGGING_CONFIG` is a bit more tricky to get. The first part is from your sentry DSN key before the `@` character. The second part is your `projectId` which is the last part of your DSN key after the last `/`. 

After you've setup all your keys sucessfully you'll need to restart appwrite. 

To do that you'll need the following commands:

```bash
$ docker-compose stop
```

```bash
$ docker-compose up -d
```

Now to get an error we have to trigger one, I looked into appwrite repo and triggered this one: https://github.com/appwrite/appwrite/issues/2722 ... Keep in mind that when you read this it might have been resolved. 😊

If the error is still present you can trigger the same error with following code snippet:

```javascript

        // Init your Web SDK
        const appwrite = new Appwrite();

        appwrite
            .setEndpoint('http://localhost/v1')
            .setProject('[ProjectID]');
        
        let promise = appwrite.database.listDocuments('[CollectionID]', undefined, 1, undefined, undefined, undefined, ["$id"], ["DESC"]);

        promise.then(function (response) {
            console.log(response); // Success
        }, function (error) {
            console.log(error); // Failure
        });
```

Example result of a logged error:   
![screencapture-sentry-io-organizations-bence-hornyak-issues-2979315844-2022-02-04-11_47_25.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1643971906082/kdQaabmGY.png)

Aaand there we go. You now succesfully setup your appwrite sentry integration.

## Closing thoughts 

As you can see it is fairly easy and quick to integrate sentry into your appwrite installation. I would suggest to give it a try! 😊

## Thank you for reading, and let's connect!

Thank you for reading my post! Feel free to subscribe to my email newsletter and connect on [LinkedIn](https://www.linkedin.com/in/bence-hornyak/) or [Twitter](https://twitter.com/b_hornyak) 😊

