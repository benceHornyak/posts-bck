---
title: "Appwrite: Versioning documents with cloud functions"
datePublished: Sun Jan 22 2023 11:34:59 GMT+0000 (Coordinated Universal Time)
cuid: cld7ayssj000809ku8pr21f0u
slug: appwrite-versioning-documents-with-cloud-functions
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ieWHXjjAEwY/upload/c4f1d4b32bf9c4ee960dd40cefce529c.jpeg
tags: cloud, javascript, beginners, appwrite

---

## Benefits 📈

Firstly, versioning allows you to undo and redo changes, particularly useful when mistakes are made or changes need to be rolled back. With versioning, you can easily revert to previous versions of data.

![](https://media.giphy.com/media/zRwUVMX5SFNWS1YBol/giphy.gif align="left")

Secondly, versioning enables better collaboration among team members. When multiple people work on the same data, it can be challenging to keep track of who made what changes. Versioning makes it clear when, where, and by whom changes were made, promoting collaboration and accountability.

![](https://media.giphy.com/media/ZoPH9i0aWiMFpvF3jF/giphy.gif align="left")

Thirdly, versioning enables data auditing, and tracking changes over time can be incredibly useful for compliance and regulatory purposes. By versioning, you can see who made what changes, when they were made, and why, making it easier to comply with data privacy and security regulations.

![](https://media.giphy.com/media/5lP4dWHYTBxYxxCRWX/giphy.gif align="left")

Lastly, versioning creates a historical record of data, allowing you to track how the data has evolved. This is useful for data analysis, troubleshooting, and understanding changes made.

![](https://media.giphy.com/media/xT9C25UNTwfZuk85WP/giphy-downsized-large.gif align="left")

Let's look at a simple implementation example of Appwrite's cloud functions with the known benefits.

To generate our started function we'll be using ***Appwrite CLI***.

## Installing Appwrite CLI

```bash
npm i -g appwrite-cli
```

This command installs the Appwrite CLI globally. It is recommended to use the CLI to sync your changes to your Appwrite instance.

## Connecting to your project

To login to your Appwrite instance you can execute the following command:

```bash
appwrite login
```

After a successful login, we can initialize a project and a function as well.

To init a project you can run:

```bash
appwrite init project
```

And our most important part for today is the functions part - for that run:

```bash
appwrite init function
```

Appwrite CLI generates an `appwrite.json` file that tracks our changes so it can be shared with others for example via git.

## Function events

Appwrite gives us a fairly good set of APIs and tools to leverage what we discussed at the beginning of the post. Functions can be triggered via *server events*, which means we can *do* stuff after certain events like documents *update* or *create*.

Let's set up our function's events property.

We can use the following format: `databases.[databaseId].collections.[collectionId].documents.[documentId].[update/create/delete]`; the ids can be changed to a `*` which means pretty much that on every database/collection/document event, our function would be triggered.  
So for our use case, we can set up the following two events:

```json
      "events": [
        "databases.63c6ed3496af20a6aa1c.collections.63c6edbae2bbf584ba7a.documents.*.update",
        "databases.63c6ed3496af20a6aa1c.collections.63c6edbae2bbf584ba7a.documents.*.create"
      ],
```

Now our function will be triggered whenever a document is created or updated which is a good start for implementing document versioning.

## Implementation

### Querying existing versions

First, we need to query existing version documents, then extract the latest version and bump it by one.

```javascript
  const data = JSON.parse(req.variables['APPWRITE_FUNCTION_EVENT_DATA']);
  const versionsCollectionId = req.variables['VERSIONS_COLLECTION_ID'];

  const alreadyExistingVersionDocs = await database.listDocuments(
    data.$databaseId,
    versionsCollectionId,
    [sdk.Query.equal('docId', data.$id)]
  );

  const version =
    alreadyExistingVersionDocs.total > 0
      ? ++alreadyExistingVersionDocs.documents[
          alreadyExistingVersionDocs.documents.length - 1
        ].version
      : 1;
```

`APPWRITE_FUNCTION_EVENT_DATA` variable contains the data that triggers this function, we need to extract some information from that.

As for listing our version documents, we need to first create an index in the version collection, if you prefer to do this in your appwrite.json here is an example for that:

```json
      "indexes": [
        {
          "key": "text",
          "type": "fulltext",
          "status": "available",
          "attributes": ["text"],
          "orders": ["ASC"]
        }
      ]
```

Bumping the version number is fairly simple: if the list request gives us anything back we bump the last version if there is no new version for this docId yet we start the version from 1.

### Creating new version

After we figured out what is the latest version of our document we need to create a new one with the `data`, `docId` and `version` .

```javascript
  await database.createDocument(
    data.$databaseId,
    versionsCollectionId,
    'unique()',
    { text: data.text, docId: data.$id, version }
  );
```

In this example, we only have a `text` attribute that needs to transfer. If you have any more properties to version you can get those from our `data` variable.

### Full solution

Here is a full example solution:

```javascript
const sdk = require('node-appwrite');

module.exports = async function (req, res) {
  if (
    !req.variables['APPWRITE_FUNCTION_ENDPOINT'] ||
    !req.variables['APPWRITE_FUNCTION_API_KEY'] ||
    !req.variables['VERSIONS_COLLECTION_ID']
  ) {
    console.error('Environment variables are not set');
    process.exit(1);
  }

  const client = new sdk.Client();
  const database = new sdk.Databases(client);

  client
    .setEndpoint(req.variables['APPWRITE_FUNCTION_ENDPOINT'])
    .setProject(req.variables['APPWRITE_FUNCTION_PROJECT_ID'])
    .setKey(req.variables['APPWRITE_FUNCTION_API_KEY'])
    .setSelfSigned(true);

  const data = JSON.parse(req.variables['APPWRITE_FUNCTION_EVENT_DATA']);
  const versionsCollectionId = req.variables['VERSIONS_COLLECTION_ID'];

  const alreadyExistingVersionDocs = await database.listDocuments(
    data.$databaseId,
    versionsCollectionId,
    [sdk.Query.equal('docId', data.$id)]
  );

  const version =
    alreadyExistingVersionDocs.total > 0
      ? ++alreadyExistingVersionDocs.documents[
          alreadyExistingVersionDocs.documents.length - 1
        ].version
      : 1;

  await database.createDocument(
    data.$databaseId,
    versionsCollectionId,
    'unique()',
    { text: data.text, docId: data.$id, version }
  );

  res.send();
};
```

## Function variables

We need to set up some function variables to be able to interact with ***node-appwrite***. Namely `APPWRITE_FUNCTION_ENDPOINT`, `APPWRITE_FUNCTION_API_KEY` and our custom one `VERSIONS_COLLECTION_ID` .

* `APPWRITE_FUNCTION_ENDPOINT` : this is your local network address, it is ideal if you can set your machine to a static IP address
    
* `APPWRITE_FUNCTION_API_KEY` : You need to generate an API key, you can do so from under your Appwrite console -&gt; overview page -&gt; API keys tab at the very bottom of the page
    
* `VERSIONS_COLLECTION_ID` : This is our collection's id for storing the versioned documents, you need to create a new collection for that and set that id here
    

You can set these variables under your functions settings tab.

## Deploying your function

To deploy your function you can run:

```json
appwrite deploy function 
```

## What's next?

At this point, this function doesn't save any information about for example who edited this document last time. There are many ways to extend and improve this behavior, stay tuned to find out more!

You can find the full [example here](https://github.com/benceHornyak/DocuReflect/tree/main/appwrite).

## Thank you for reading, and let's connect!

Thank you for reading my post! Feel free to subscribe to my email newsletter and connect on [LinkedIn](https://www.linkedin.com/in/bence-hornyak/) or [Twitter](https://twitter.com/b_hornyak) 😊