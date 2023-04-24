---
title: "Commonly used http methods"
seoTitle: "Commonly used http methods"
seoDescription: "Commonly used http methods with short examples"
datePublished: Tue Mar 15 2022 19:51:54 GMT+0000 (Coordinated Universal Time)
cuid: cl0sjw82f027t5anvh1dd0cu9
slug: commonly-used-http-methods
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1644007184922/WXxUDUQGWX.png
tags: http, web-development, backend, beginners, 2articles1week

---

As I've experienced one of the most commonly asked question for a software enigneer position is about HTTP methods, doesn't matter if you are applying for frontend or backend position, both sides should understand the differences and use these methods properly while either building or consuming an API.

So here are the five most commonly used HTTP methods and what you should know about them! 

## GET 🔎

`GET` is used for retrieving **multiple resources** or retrieving a **single resource**. 

### Examples

#### List of resource

```
/api/user
```

Would return a **list** of user resource. Here in our context a resource is a single row in a database.

You can build pagination and simple search functionality with **query strings**: 

```
/api/user?page=1&pageSize=5&hasProfilePicture=true
```

#### Single resource

```
/api/user/1337
```

Would return **a single** user resource which has an id of `1337`. 

## POST 📬

`POST` is used for **creating** new resources. Each request shall **create a new resource**. 

### Example

For example to create a new user we could do:

```
/api/user
```

With the body of:

```json
{
  username: "great username",
  password: "super-safe-password"
}
```

Of course in this case the password needs to be hashed.


## PUT ♻️

`PUT` is used for **replace resource with the sent one**. It can be also used to create new resource, but by RFC it should only replace resource and not create new one. 

### Example

```
/api/map/1337
```

With the body of: 

```json
{
  lat: 42.42,
  lng: 42.42
}
```

This would completely replace the resource with the id of 1337 with the new - sent - data. 


## PATCH 🩹

`PATCH` is used for replace **some** part of the original resource. Should be used as **partial updates**. 

### Example

```
/api/map/1337
```

With the body of: 

```json
{
  lat: 42.42
}
```

The difference between `PUT` and `PATCH` is that `PUT` replaces the whole resource with the sent one while `PATCH` only changes what is sent. 

## DELETE 🗑️

`DELETE` is used for **deleting a resource**. 

### Example

```
DELETE /api/map/1337
```

Would delete map with the id of `1337` if exist. 

## Bonus question ➕

### Which are idempotents and what is idempotent? 

Idempotent means that **multiple requests** can be made with the **server state unaffected**. In other words idempotent requests should **NOT** have any side effect, it shouldn't change the server state. 

In this sense only `POST` is **NOT** idempotent, since `POST` is creating new "database rows" on every request. 

## Closing thoughts 

Thanks for reading. Please do keep in mind that technically there are no restrictions for creating these APIs so you can customize each and every behaviour. 

## Thank you for reading, and let's connect! 

Thank you for reading my post! Feel free to subscribe to my email newsletter and connect on [LinkedIn](https://www.linkedin.com/in/bence-hornyak/) or [Twitter](https://twitter.com/b_hornyak) 😊



