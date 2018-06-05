---
layout: post
title: 'Linq and SQL Collation with Entity Framework'
tags: 
- EF
- Entity Framework
- SQL
- Linq
- Code First
- Graph
---

Using MS exellent Graph API to ftch emails I suddenly became aware of duplicates when trying to add the messages to a db.

When fetching 50 email, EF reported duplicates on the same _id_ when 
checking db with this statement:

{% raw %}
```C#

var exist = await db.Emails.FirstOrDefaultAsync(_ => _.Id== m.Id);


```

These _id_ strings are quite long, around 160 chars, and to me and EF they looked similar.

But when checking it with a _Dictionary_ I could find no duplicates.
So I checked it more thorough and found that the key could vary only by case on a single character:
```code
 
AAMkAGE4MjM4NTAxLTY3ZTUtNGY4NC04ZGI0LTM2MWVhYzEzMjRlNwBGAAAAAADpZpPX7Q2dTpugZn8x1wszBwB3u7vk3DktTqKitD-iexH4AAAAAAEMAAB3u7vk3DktTqKitD-iexH4AABp_ej4AAA=

AAMkAGE4MjM4NTAxLTY3ZTUtNGY4NC04ZGI0LTM2MWVhYzEzMjRlNwBGAAAAAADpZpPX7Q2dTpugZn8x1wsZBwB3u7vk3DktTqKitD-iexH4AAAAAAEMAAB3u7vk3DktTqKitD-iexH4AABp_ej4AAA=

```

So a Z is lowercase in the first and uppercase in the second.

To make a long debugging session a bit shorter,

##I had to change my SQL Server collation to a case-sensitive one.##

I chose Norwegian_100_CS_AI and added it to my initial migration before 
Create Table etc 
```C#
        public override void Up()
        {

            Sql("ALTER DATABASE CURRENT COLLATE Norwegian_100_CS_AI", suppressTransaction: true);

```


Now the same check:
```C#

var exist = await db.Emails.FirstOrDefaultAsync(_ => _.Id== m.Id);

```

can find different emails based on their string _id_



PS. Graph API and also Graph SDK for C# are excellent resources for all kinds of interesting functionality on top of Microsofts stack of Business Solutions like Exchange, SharePoint and Office 365.
