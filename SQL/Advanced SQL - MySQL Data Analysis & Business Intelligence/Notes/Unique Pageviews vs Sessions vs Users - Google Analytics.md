> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.digishuffle.com](https://www.digishuffle.com/blogs/unique-pageviews-vs-sessions-vs-users/)

> Understand Comparison between Unique Pageviews vs Sessions vs Users. Why total Unique pageviews ≠ Tot......

No Comment Feb 16, 2017 [Web / Google Analytics](https://www.digishuffle.com/blogs/category/web-google-analytics/)

Today, we will be understanding the Unique Pageviews, Sessions and Users Metrics and also some questions like:

*   What is the relation between Unique Pageviews and Sessions?
*   How Unique pageview and Sessions are attributed to a page dimension?
*   Why Total Unique pageviews are not equal to Total sessions?
*   Why Unique pageview of a page > Total sessions?  
    …and more

Click Below To Try **[NEW] Google Analytics Simulation.**

[Google Analytics Simulation - Try Now!](https://www.digishuffle.com/blogs/unique-pageviews-vs-sessions-vs-users/#ga-simulation) ![](https://www.digishuffle.com/wp-content/uploads/2017/12/GASimulation.gif) Google Analytics Simulation - Users vs Pageviews vs Sessions

**So, What is Unique Pageviews?**
---------------------------------

It is

_**The number of sessions in which the specified page was viewed one or more times.**  
_

Many a times its confusing that **[‘unique pageviews’ definition has a session](https://www.digishuffle.com/blogs/practical-guide-to-unique-pageviews-vs-pageviews-metrics-simplified-series/) word in it.**

I’ll be taking the same example from the previous post

**Experiment 1:**

So, a bit of recap, a single user has visited the 5 page in a sequential order in one session:

1.  /page-1
2.  /page-2
3.  /page-3
4.  /page-1
5.  /page-2

Now in Audience overview, you’ll see:

[![](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews2.png)](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews2.png)

While in Behavior > Site content > All pages, you’ll see:

[![](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews1.png)](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews1.png)

**Observations:**

*   Here, 1 user had 1 session with 5 pageview and 3 unique pageviews.
*   Remember that the **unique pageview is session level metric.**  
    So the /page-1 was viewed (We don’t care how many times?) in one session .
*   Similarly, /page-2 and /page-3 were viewed in one session.

Don't make mistake of adding the unique pageviews (3) and comparing with the total sessions (1). They are totally different.  
The sum of unique pageviews of all pages have overlapping sessions (i.e in same sessions 1+ pages were viewed).

Unique Pageviews vs Sessions vs Users Report:
---------------------------------------------

My first recommendation here would be **not to create the following report which most people do and then get confused**:

[![](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews5.png)](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews5.png)

**Observations:**

This report combines **Hit level Dimension (Page) with Sessions level and hits level metrics.** Let me sum up the things happening here:

*   The **sessions get attributed to the landing page or entrance page. Here, I have also shown entrance page to verify.**
*   **Unique pageviews metric is like sessions attributed to all pages viewed in that particular session.**
*   **Users get attributed to all pages.**

That is why, you should always use **Unique pageviews as a metric with page dimension rather than sessions.**

I hope you are getting the relation between **Unique pageviews vs Sessions vs Users.**

Lets consider the following scenario.

**Experiment 2:**

Now lets say, the same user return the next day and follows the sequential visits as:

1.  /page-1
2.  /page-2
3.  /page-1
4.  /page-2

The Audience Overview report would show:

[![](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews3.png)](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews3.png)

The all pages report would show:

![](https://www.digishuffle.com/wp-content/uploads/2017/02/Metrics-Unique-Pageviews4.png)

**Observation:**

*   You can see that the user is the same, so the audience overview report for those 2 days will show 1 users and 2 sessions.
*   In all pages report, you can see that the /page-1 and /page-2 were viewed in 2 sessions but /page-3 was only viewed in 1 session. (We really don’t care about users here, its just sessions.!!)

The total unique pageviews is not equal to total sessions (5≠2).  
So, most of the times

*   Total Sessions < Total Unique Pageviews
*   Unique Pageviews < Pageviews.

What is more important in analysis - Unique Pageviews or Sessions ?
-------------------------------------------------------------------

Well, It depends on the context.

####   
Unique Pageviews: 

It is mostly used to answer **page (or page groups) specific questions in a specified date range**. For Eg:

*   What are the **Unique Views per User** for a **particular page X (or page group X)**? (**Ans**: **Unique Pageviews / Users**) 
*   How many sessions did a **particular Page X (or page group X) had**?  
    (**Ans** : **Unique Pageviews**)
*   What is the **conversion rate of a page? (Ans: Users Converted / Users who saw the page**)

Don’t use ‘**Session’ metric with ‘Page’ as dimension**. In the below reports, the **‘sessions’ are simply the first pageview or (interaction) events attributed to landing page.** 

![](https://www.digishuffle.com/wp-content/uploads/2017/12/Users_vs_Sessions_vs_Unique_Pageview_-_Google_Analytics.png)

#### Session: 

It is mostly used to analyze **whole** **site experience (including all pageviews, events, goals/ transactions) in a specified date range**. For Eg:

*   What source of **traffic has low Bounce Rate**?  (**Ans:** **Bounced Session / Total Sessions)**
*   How many **% sessions** are there for a **particular source/medium/campaign**?**(Source|Medium|Camapign Sessions) / Total Sessions**)
*   What is the **conversion rate of a particular source/medium/campaign ? (Converted Sessions / Total Sessions)**
*   What is the % sessions for Desktop vs Mobile ? (**Desktop Sessions / Total Session** ) 
*   What are **total events / sessions** ? & so on..

Conversion Rate: UniquePageviews Or Sessions Or Users ?
-------------------------------------------------------

Google Analytics calculates conversions rate by taking ‘**sessions**‘ into account. So the formula is

**Conversions Rate = Goal Completions / Sessions**

Here’s the drawback of this approach…

if we **acquire 1000 sessions but only 300 saw the offer page.** Google analytics will consider **1000 sessions while calculating** **conversions rate**. 

So, Here’s the **real formula** to use 

_**Conversions Rate = Goal Completion / Unique Pageviews**_

OR even better

_**Conversions Rate = Goal Completion / Users**_

Here, **Unique Pageview will ensure the sessions who visited the offer page are considered**. 

Similarly, **‘Users’ will ensure the total users who saw the offer page are considered**.

You can utilize [calculated metrics](https://support.google.com/analytics/answer/6121409?hl=en) for defining these metrics.

Compatible Dimensions for Unique Pageviews & Sessions
-----------------------------------------------------

For ‘**Unique Pageviews’ Metric** use the following dimensions

*   **Page**
*   **Content/Page Group.**
*   **Page Title**
*   **& so on..**

For ‘Session’ Metric you can use

*   **Source**
*   **Medium**
*   **Device**
*   **Hostname**
*   **Landing Page**
*   **OS / Screen Resolution**
*   **Browser**
*   **& so on..**

Google Analytics Simulation - Pageviews vs Unique Pageviews vs Users vs Sessions
--------------------------------------------------------------------------------

*   Kindly Try the below [google analytics simulation](https://www.digishuffle.com/ga-metric-simulator/) to clearly understand **How Google Analytics Reports Data**.
*   Assume that All the below event take place in a **same date range**.

_**Note: This Simulation is Only Visible To You. It is not saved or stored. Feel Free To Use It**_

User A B C D Visits Page URL :  in Same Session  Add Page Reset

Users Sessions

<table><thead><tr><th>Page</th><th>Pageviews</th><th>Unique Pageviews</th><th>Users</th><th>Sessions</th></tr></thead><tbody></tbody></table><table><thead><tr><th>No.</th><th>User</th><th>Page</th><th>New Session?</th></tr></thead><tbody></tbody></table>

FAQs
----

#### What is Unique Pageview in Google Analytics?

*   As [defined by google](https://support.google.com/analytics/answer/1257084?hl=en), A _unique pageview_ represents the number of sessions during which that page was viewed one or more times.

#### What are Pageviews in Google Analytics?

*   Pageviews are the total number of pages viewed. Repeated views of a single page are counted. If a user navigates to a different page and then returns to the original page, a second pageview is recorded as well.

#### What is the difference between Unique Pageview & Pageviews in Google Analytics?

*   A **_Pageview_** is defined as a view of a page on your site that is being tracked by the Analytics tracking code. If a user clicks reload after reaching the page, this is counted as an additional pageview.
*   While a **_Unique Pageview_** represents the number of sessions during which that page was viewed one or more times.”

#### What is the difference between Sessions & Pageviews in Google Analytics?

*   **_As per [Google](https://support.google.com/analytics/answer/1257084?hl=en), Sessions_** represent the number of individual sessions initiated by all the users to your site. If a user is inactive on your site for 30 minutes or more, any future activity is attributed to a new session. Users that leave your site and return within 30 minutes are counted as part of the original session.
*   While a **_Pageview_** is simply a counter for page load. If page re-loads, it is counted as another pageview.

Summary
-------

*   Most of the times **Unique Pageview of a particular page <=  Total Sessions on website**.  
    Sometimes this might not be the case because the **non-interaction pageview hit counts unique pageviews but doesn’t count sessions**. This results in **Unique Pageview of a particular page >  Total Sessions on website.**
*   Don’t apply **session level metrics for hit level dimensions like page.** Use similar scope dimension and metrics.

  
To know more about types of hits, I recommend you to read:  
[Google Analytics – Sessions & Hits – Metrics Simplified Series](https://www.digishuffle.com/blogs/google-analytics-sessions-hits-metric-simplified/).

[Google Analytics – Users – Metrics Simplified Series](https://www.digishuffle.com/blogs/google-analytics-users-metric-simplified/)

[Google Analytics – Bounce Rate vs Exit Rate – Metrics Simplified Series](https://www.digishuffle.com/blogs/bounce-rate-exit-rate/)

[Google Analytics – Count Of Sessions – Metrics Simplified Series](https://www.digishuffle.com/blogs/google-analytics-count-of-sessions-metrics-simplified-series/)

More Articles:

**[Bounce Rate Bible: Bounce Rate Benchmarks](https://www.digishuffle.com/blogs/bounce-rate-benchmarks/)**

[**Bounce Rate Bible: 15 Ways To Reduce High Bounce Rates in Google Analytics**](https://www.digishuffle.com/blogs/bounce-rates-google-analytics/)

**[Automated Cost Data Import – Facebook To Google Analytics](https://www.digishuffle.com/blogs/cost-data-import-facebook-to-google-analytics/)**

**[Google Analytics Audit Checklist – Complete Guide](https://www.digishuffle.com/blogs/google-analytics-audit/)**

 [![](https://secure.gravatar.com/avatar/f332fe9a1485b8bf40163b4109dcf52a?s=120&d=mm&r=g)](javascript:;) 

##### [Ritwik B](https://www.digishuffle.com/blogs/author/ritwikga/ "Posts by Ritwik B")

[](https://www.digishuffle.com/blogs/unique-pageviews-vs-sessions-vs-users/)[](https://www.digishuffle.com/blogs/unique-pageviews-vs-sessions-vs-users/@RitwikGA)[](https://www.digishuffle.com/blogs/unique-pageviews-vs-sessions-vs-users/)[](https://www.digishuffle.com/blogs/unique-pageviews-vs-sessions-vs-users/)

Ritwik is a Web Analyst & Product Marketer. He loves to write technical & easy to understand blogs for Marketers & Entrepreneurs. Focused on Google Analytics, Facebook Analytics, Tag Management, Marketing & Automation Scripts & more. Google Certified Professional. A Firm Believer in Teaching -> Learning -> Growing. :)