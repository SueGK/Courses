# Analyze Traffic Source

![[Pasted image 20220518221549.png]]

## Finding Top Traffic Sources
```SQL
USE mavenfuzzyfactory;
SELECT 
	utm_source,
    utm_campaign,
    http_referer,
	COUNT(DISTINCT website_session_id) AS sessions
FROM website_sessions
WHERE created_at < "2012-04-12"
group by 
	utm_source,
    utm_campaign,
    http_referer
ORDER BY 4 DESC -- 4 MEANS COLUMN 4
```

## Traffic Source Conversion Rates
```sql
USE mavenfuzzyfactory;
SELECT 
	COUNT(DISTINCT orders.order_id) AS orders,
	COUNT(DISTINCT website_sessions.website_session_id) AS sessions,
    COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT website_sessions.website_session_id) AS session_to_order_conv_rate
FROM website_sessions
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
WHERE website_sessions.created_at < "2012-04-14"
	# 别忘了指定traffic source	
	AND utm_source = 'gsearch'
	AND utm_campaign = 'nonbrand' 
GROUP BY utm_source, utm_campaign
ORDER BY session_to_order_conv_rate DESC

```

## Traffic Source Trending
>Based on your conversion rate analysis, we bid down gsearch nonbrand on 2012-04-15. 
Can you pull gsearch nonbrand trended session volume, by week, to see if the bid changes have caused volume to drop at all?

```SQL 
SELECT
	-- YEAR(created_at) As yr,
	-- WEEK(created _at) As wk,
	-- YEARWEEK(created_at) AS year_week
	MIN(DATE(created_at)) AS week_started_at,
	COUNT (DISTINCT website_session_id) As sessions
FROM website_sessions
WHERE created_at ＜ '2012-05-12'
	AND utm_source ='gsearch'
	AND utm_campaign = 'nonbrand'
GROUP BY
-- you can group even though you dont have this in your select statement
	YEAR(created_at),
	WEEK(created_at)
```
![nXyKrd](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/nXyKrd.png)

⚡ <span style='color:red;font-weight:bold'>ATTENTION</span>
* GROUP BY 要按year and week来，如果只按week，会把不同年的月份数据相加造成混乱

## PIVOTING  DATA WITH COUNT&CASE
![](https://firebasestorage.googleapis.com/v0/b/firescript-577a2.appspot.com/o/imgs%2Fapp%2FFemFarm%2F8sUAw_nX9e.png?alt=media&token=667c9e46-17d6-4c86-883b-43e6e4192582)

>Could you pull conversion rates from session to order, by device type?

![M8huvD](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/M8huvD.png)
Then we're going to split total sessions into two columns
![PvOOp2](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/PvOOp2.png)


# Analyze Website Performance
## CREATING TEMPORARY TABLES
![a67Zu7](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/a67Zu7.png)

## The most common pages in general
![dKluao](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/dKluao.png)

## The top entry pages 

每个session中
![[Pasted image 20220519032648.png]]
### Step1: find the first pageview for each session
💡 <span style='color:red;font-weight:bold'>建立在session上</span>
****
```SQL
CREATE TEMPORARY TABLE first_pv_per_session
SELECT 
	website_session_id,
	MIN(website_pageview_id) AS first_pv
FROM website_pageviews
WHERE website_pageview_id < 1000 
GROUP BY website_session_id;
```
### Step2: find the url the customer saw on that pageview
```SQL
SELECT 
	pageview_url AS landing_page_url, -- AKA "entry page"
	COUNT(DISTINCT fp.website_session_id) AS sessions_hitting_page
FROM first_pv_per_session fp
	LEFT JOIN website_pageviews wp
    ON fp.first_pv = wp.website_pageview_id
GROUP BY pageview_url
```
![pZaBay](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/pZaBay.png)


website_sessions table中created_at 是每个session开始的时间
（ex：website_session_id为175251）
![Wi86y6](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/Wi86y6.png)

website_sessions table中created_at 是打开每个page的时间
（website_session_id为175251对应3个pageview）
![vw34Pd](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/vw34Pd.png)

📌  两个created_at并不相同，
题目要求**created_at BETWEEN '2014-01-01' AND '2014-02-01'**, 此时的created_at代表的是website_sessions table中的，因为我们要研究的是landing page，即每个session开始时的entry page。所以不能如下所示： <span style ='color:purple;font-weight:bold'>直接在website_pageviews table利用where filter 时间</span>

```SQL
CREATE TEMPORARY TABLE first_pageviews_demo
SELECT
	website_session_id,
    MIN(website_pageview_id) AS first_pv_id 
FROM website_pageviews
WHERE created_at BETWEEN '2014-01-01' AND '2014-02-01'
GROUP BY website_session_id;
```

<span style ='color:purple;font-weight:bold'>需要INNER JOIN website_sessions table，并且在join的时候就利用on直接filter sessions table中的时间然后join</span>

```sql
SELECT
	wp.website_session_id,
    MIN(wp.website_pageview_id) AS first_pv_id 
FROM website_pageviews wp
INNER JOIN website_sessions ws
	ON wp.website_session_id = ws.website_session_id
	AND ws.created_at BETWEEN '2014-01-01' AND '2014-02-01'
GROUP BY wp.website_session_id;
```

## Analyzing Bounce Rates & Landing Page Tests

**-- same query as above, but this time we are storing the dataset as a temporary table**
```sql
CREATE TEMPORARY TABLE first_pageviews_demo
SELECT
	wp.website_session_id,
    MIN(wp.website_pageview_id) AS first_pv_id 
FROM website_pageviews wp
INNER JOIN website_sessions ws
	ON wp.website_session_id = ws.website_session_id
	AND ws.created_at BETWEEN '2014-01-01' AND '2014-02-01'
GROUP BY wp.website_session_id;
```
![Ypj6MF](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/Ypj6MF.png)

**-- next, we' 1l bring in the landing page url to each session**

```SQL
CREATE TEMPORARY TABLE sessions_w_landing_page_demo
SELECT
	fpd.website_session_id,
    wp.pageview_url AS landing_page
FROM 
	first_pageviews_demo fpd
	LEFT JOIN website_pageviews wp
		ON fpd.first_pv_id = wp.website_pageview_id;
```
![hgNba2](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/hgNba2.png)

**-- next, we make a table to include a count of pageviews per session
-- first, T'il show you all of the sessions. Then we will limit to bounced sessions and create a temp table**

```SQL
CREATE TEMPORARY TABLE bounced_sessions_only
-- SELECT * FROM sessions_w_landing_page_demo;
SELECT 
	slp.website_session_id,
    slp.landing_page,
    COUNT(wb.website_pageview_id) AS count_of_pages_viewed
FROM sessions_w_landing_page_demo slp
	LEFT JOIN website_pageviews wb
		ON slp.website_session_id = wb.website_session_id
GROUP BY
	slp.website_session_id,
    slp.landing_page
# CHOOSE BOUNCE
HAVING COUNT(website_pageview_id) = 1;

SELECT 
	slp.landing_page,
    slp.website_session_id,
    bso.website_session_id AS bounced_website_session_id
FROM sessions_w_landing_page_demo slp
	LEFT JOIN bounced_sessions_only bso
		ON slp.website_session_id = bso.website_session_id
ORDER BY slp.website_session_id;
```
![b2PdIt](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/b2PdIt.png)

**-- Final output
	-- we will use the same query we previously ran, and run a count of records
	-- We will group by landing page, and then we' ll add a bounce rate column
```SQL
SELECT 
	slp.landing_page,
    COUNT(DISTINCT slp.website_session_id) AS sessions,
    COUNT(DISTINCT bso.website_session_id) AS bounced_sessions,
    COUNT(DISTINCT bso.website_session_id)/COUNT(DISTINCT slp.website_session_id) AS bounce_rate
FROM sessions_w_landing_page_demo slp
	LEFT JOIN bounced_sessions_only bso
		ON slp.website_session_id = bso.website_session_id
GROUP BY 1
ORDER BY 1;
```
![VyhRDD](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/VyhRDD.png)

And so in this case, what we see is Lander three has a bounce rate of about sixty one and a half percent where the home page and lander to have bounce rates of thirty eight percent and about forty four percent.

So it's important to note that you can't totally just use bounce rate to judge this.

One landing page may serve a segment of traffic that is particularly low quality, for example, display advertising where another landing page may serve, say, paid search, which is a lot higher content customer that's already hand raised for your product.

So you kind of have to think about those things in terms of analyzing bounce rates and landing pages.

# [[37-Analyzing Bounce Rates & Landing Page Tests]]

# [[42-Landing Page Trend Analysis]]

# [[44-Building Conversion Funnels & Testing Conversion Paths]] 

# [[47-Analyzing Conversion Funnel Tests]]
# [[Mid Course Project]]