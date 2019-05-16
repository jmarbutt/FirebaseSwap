## Why I swapped to Firebase from Azure
This document is to cover all the reasons I decided to move to Firebase for my company and my products. 

### A little about me
I have been a long time .net developer (since the original betas) and pretty much have only focused on .net. I have been an speaker, user group arranger, author and heavily involved in the .net world for a long time. So moving to a new platform was a hard decision but so far it has had great rewards. 

### What I love about Firebase
There are so many things that google has done really well with Firebase that I am just going to start with the ones that pushed me over the edge:

#### One Product
Firebase is offered as a **single** product with various features. This can not be understated and should not be ignored. It is simple and removes a lot of the guess work in pricing and which features you might need. 

#### Great SDK
The SDK for `@angular/fire` is overly simple. No need to write complex apis, DTOs, ect. It can be handled directly from the client.

#### CDN Hosted App
With the web app actually being hosted on edge networks I get extremely fast response times. 

#### Fast Database
Let's just put it this way, Firestore is fast! It is so much faster than I expected.

#### Speed of Development  
It is insanely easy to get started with Firebase. No need for an API project, a database, ect. It is all built in. 

#### Less to Manage/Less infrastructure
Compared to Azure, where I feel like I am spending a lot to manage my own infrastructure, with Firebase there is almost nothing to manage. I simply say I want to create a project primarily hosted in the US. I don't have to mirror the db or manage any of that. 

***Think about it*** this is a huge shift from the focus of Azure. Being able to truly focus on development is huge. While Azure got us a lot further from managing our own data centers or servers stuck in a closet, this is the next step. 

#### Consistent Authentication setup
It is extremely easy to get authentication information from the top all the way down to the db level. For example, if you need to check the authentication level for a user you can do something this simple:

```js
match /accounts/{accountId}{
      function getRole(role) {
        return get(/databases/$(database)/documents/accounts/$(accountId)/users/$(request.auth.uid)).data[role];
      }
      
      allow read: if getRole('user') == true;
      allow update: if getRole('user') == true;
      allow create;
      allow delete: if getRole('user') == true;
      
      match /{document=**} {

        allow read: if getRole('user') == true;
        allow update: if getRole('user') == true;
        allow create: if getRole('user') == true;
        allow delete: if getRole('user') == true;
        
      }
      
    }
```

The authenticated user comes from the client api. No plumbing needed. 

In addition to DB rules, the **storage** rules work the same way! Let me repeat, the storage rules are almost identical to the database rules. 

#### No Need for Middle Tier
Getting rid of the middle tier is so much easier and much more serverless than anything azure offers. I can have my client directly update the database and everything works. The security is based on security rules I have put in place. 

When a need arises to do more than something basic, you can use Firebase Functions. By removing the middle tier you take out a lot of the need for the things that come with dotnet. 

#### Love for @angular/fire
Since I mostly do angular, using angular fire is extremely refreshing. Doing things like binding to a database is super easy. Like 1 line of code easy:

```ts
let myList: Observable<MyItem> = db.collection('my-items').valueChanges();
```

#### Realtime Database
Now that you have seen how easy it is to bind, did I mention that the observable gets fired any time the db changes? No polling, ect.

#### Firebase Functions
Firebase Functions has a much more polished and consistent structure with the rest of the firebase product. Things like the ability to get the authenticated user on a database update or when a file changes is **HUGE**. No longer do I need to pass all this around and wonder if I am introducing a new security hole. 

#### Simple Pricing
The simplicity of the pricing is also a big benefit. Compared to Azure & AWS products, it is easy to understand and easy to manage. 

#### Great Community 
There is a great community around Firebase and it is growing rapidly. From sites like fireship.io and angularfirebase.com to the slack communities and Google's own video series. 

#### Google's Video Series
I want to take a moment and mention how big of an impact google's video series has had on my decision. It is polished at a different level than most. Their #AskFirebase and #Firecasts series are super polished. I think my favorite videos are the Get To Know Firebase series. They teach best practices in an entertaining and new way. 

### Things I don't love about Firebase
With everything I love it isn't perfect of course. So I figured I would let you know a few things I don't love. 

#### Vendor Lock In
Not being able to take my app anywhere is a pretty big deal. Or if Google changes direction, I am in deep trouble. 

#### Security Rules could be better
While the security rules on the db and storage are light years ahead of other things I have used. The ***recommended*** way to do validation is also in the security rules which is kind of wrong to me. It would be great to see that as a different feature with in a collection.

#### Firestore Queries
While firestore is great and they have come a long way in querying, it is still not sql. I do miss easy aggregation, joins, and more. 

#### No Point in Time Backups
Unlike SQL Azure, there are no point in time backups.

----
## Why not Azure?
So while this document has been mainly about what I love about Firebase, let me dive into some details what has pushed me away from Azure.

### Complex Pricing
I think enough can't be said about this but the pricing model is way too complex. For a small company I don't want a surprise of a $500 bill for Cosmos DB that I partitioned wrong with 10 rows in it (this happened to me and they refunded). 

I want to take a moment to rant about the pricing...

- By following many best practices set by microsoft, azure gets very expensive. Whether it is the partitioning of Cosmos, mirroring of SQL dbs, scaling apps, ect. 
- Additional Support should not be that expensive. Google's support for less than 1 response is $250 which is 1/4 Microsoft's. 
- DTUs vs vCore, RUs - These are all things I don't have time to figure out and don't want to. Estimating costs is almost impossible. It shouldn't be this complicated.  


### Slow Development
I love dotnet core and sql server I can do it in my sleep. But let's face it, it is just so much slower to develop for. If you take a simple example of adding a field in a typical dotnet solution you may have the following steps (and I think this is a dumb down version):
1. Add To EF Model
2. Add Migration
3. Update Database
4. Add to DTO
5. Add to UI

While there are varying degrees to thi scenario, I think you can get the point. I have seen in some consulting projects I have worked with that they had a 20-30 step process just to add a field! I typically try to simplify any project like that I come into but this is very very rampant in dotnet projects. 

### Too Many Products
There are too many pieces that I have to get up to speed with, each with their own terminology, issues, sdks and quirks. It is also obvious that most of the products are built by various teams with inconsistent visions and goals. These issues have gotten better over the years (far cry from the days of the LINQ to SQL vs Entity Framework ) but it is not a single voice. 

### SQL Performance
We spend a TON a month for our SQL server and I am just done with it. We always have to bump it up where we are spending about $1k a month on a sql db that is about 60gb. 

### Web App Scaling
While it is great that the web apps scale but I no longer need that for a angular app. I want to host on a CDN in a simple way. Google has mastered this. 

### Storage
I use azure storage blobs pretty heavily for our app. But we are using about 7 TB of data and it is just tough to manage. 

### Inconsistency 
Because of the number of products there are inconsistent sdks. Whether it is trying to use an SDK in `node` vs `dotnet core` you see a ton of differences. There also have been a number of times where I was looking for specific features or specific sdk and it was in a version that didn't work or had major issues. I think I mostly saw this around the node sdks but I haven't used node in a while on a project to make a great judgement on that. 

### Caters to Large Enterprise
It doesn't take long to see how microsoft really leverages their products for large enterprises. I am a part of a couple small companies making a big impact some which will stay on Azure but I am always looking for opportunity to move to Firebase.

## It's not all bad on Azure
There are so many great things going on with Azure:

- CosmosDB - This is a huge leap forward towards the answer with Graph, document db and SQL all in one. If the pricing and structure can be simplified, this would be a huge winner. But until that pricing is in terms a human can understand, I will stay away.

- SQL Azure - While CosmosDB is a huge leap forward, I have great faith in SQL Azure. It is just rock solid solution when it comes to a sql database.

- Azure DevOps - I love all that has been done with Azure DevOps, from pipelines to how far even issue tracking has come. This is something to truly be proud of.

- GitHub - I mean what else can be said, this was a good purchase and looking forward to what else this will bring.

## Final Thoughts on Microsoft
Like I said, I have a been in the dotnet world forever. I love Microsoft for the most part but it still has some room to grow for products and smaller companies. The vision has gotten clear over the years with new leadership. I hear enough from people I know that it isn't the same Microsoft that it was a few years ago. I hope that it continues to get better and maybe we will be back for these products. 
