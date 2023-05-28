---
layout: post
title:  "Factory consider db"
summary: "Factory consider db"
author: thanhtinhpas1
date: '2023-05-28 14:35:23 +0530'
category: jekyll
thumbnail: 
keywords: Factory consider db
permalink: /blog/factory-consider-db
usemathjax: true
---

# Factors to consider in database selection

![Factors selection](https://substack-post-media.s3.amazonaws.com/public/images/d05f286e-16e5-457c-aee3-cc6b960c796d_1600x755.png)


## Scability
Different database types handle scaling in various ways, we must first understand its architecture and design principles. From that, we can have the best decitions for our systems.

Relation database, store database in predefined schema, and they struggle to scale horizontally due to the need to maintain consistency in distributed environment.

Otherwise, NoSQL was born for horizontal scaling, so it more efficient to scale horizontally and bettern handler the growing data and traffic loads.

NewSQL databases aim to combine the consistency ability of Relation database and the powerful of scaling as TiDB. You can see the design principle behind it [here](https://pingcap.medium.com/five-principles-that-guide-tidb-and-pingcap-part-i-71bcb1f84c0)

## Consistency

![Cap theorem](https://hazelcast.com/wp-content/uploads/2021/12/cap-theorem-diagram-800x753-1.png)

Depending on your application, you may want to the data change events was consiststent or eventually consistency.

NoSQL database often lean towards eventual consistency, the update process will eventually propagat accross all nodes in the system, but they might not immediately visible.

![](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F5ebe2481-4e5e-4c80-9a8d-a9caf8255a45_1600x1361.png)

## Data Model
Relation database using fixed schema. It enforces a consistent structure across different nodes. How ever it can be a limited for your system if the system should dealing with diverse or rapidlly changing data schema, as schema changes can be time-consuming and may require downtime.
