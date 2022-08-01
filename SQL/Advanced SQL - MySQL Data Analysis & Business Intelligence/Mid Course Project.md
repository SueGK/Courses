#mysql #题目 #Course/AdvancedSQL 
![r0d0bn](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/r0d0bn.png)
![wYkJKH](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/wYkJKH.png)


## Q2
![ci0Yhq](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/ci0Yhq.png)

![V3bIZa](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/V3bIZa.png)


```sql
/*
1. Gsearch seems to be the biggest driver of our business, pull monthly trends for gsearch sessions and orders
*/

select 
	year(ws.created_at) as created_year,
	month(ws.created_at) as created_month,
    COUNT(DISTINCT ws.website_session_id) AS sessions,
    COUNT(DISTINCT od.order_id) AS orders,
    COUNT(DISTINCT od.order_id)/COUNT(DISTINCT ws.website_session_id) AS conv_rate
from website_sessions ws
left join orders od
	using(website_session_id)
where ws.utm_source = 'gsearch'
	and ws.created_at < '2012-11-27'
# group by year and corresponding months
group by 1,2;

/*
2. a similar monthly trend for gsearch, split out nonbrand and brand campaign separately
*/
select 
	year(ws.created_at) as created_year,
	month(ws.created_at) as created_month,
	ws.utm_campaign,
    COUNT(DISTINCT ws.website_session_id) AS sessions,
    COUNT(DISTINCT od.order_id) AS orders,
    COUNT(DISTINCT od.order_id)/COUNT(DISTINCT ws.website_session_id) AS conv_rate
from website_sessions ws
left join orders od
	using(website_session_id)
where ws.utm_source = 'gsearch'
	and ws.created_at < '2012-11-27'
# group by year and corresponding months
group by ws.utm_campaign, created_year, created_month;

#pivoting the table using COUNT CASE WHEN
select 
	year(ws.created_at) as yr,
	month(ws.created_at) as mo,
	COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN ws.website_session_id ELSE NULL END) AS nonbrand_sessions,
    COUNT(DISTINCT CASE WHEN utm_campaign = 'nonbrand' THEN od.website_session_id ELSE NULL END) AS nonbrand_orders,
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN ws.website_session_id ELSE NULL END) AS brand_sessions,
    COUNT(DISTINCT CASE WHEN utm_campaign = 'brand' THEN od.website_session_id ELSE NULL END) AS brand_orders
from website_sessions ws
left join orders od
	using(website_session_id)
where ws.utm_source = 'gsearch'
	and ws.created_at < '2012-11-27'
# group by year and corresponding months
group by 1,2;



/*
3. split by device type for nonbrand
*/
select 
	year(ws.created_at) as yr,
	month(ws.created_at) as mo,
	COUNT(DISTINCT CASE WHEN device_type = 'desktop' THEN ws.website_session_id ELSE NULL END) AS desktop_sessions,
    COUNT(DISTINCT CASE WHEN device_type = 'desktop' THEN od.website_session_id ELSE NULL END) AS desktop_orders,
    COUNT(DISTINCT CASE WHEN device_type = 'mobile' THEN ws.website_session_id ELSE NULL END) AS mobile_sessions,
    COUNT(DISTINCT CASE WHEN device_type = 'mobile' THEN od.website_session_id ELSE NULL END) AS mobile_orders
from website_sessions ws
left join orders od
	using(website_session_id)
where ws.utm_source = 'gsearch'
	and ws.utm_campaign = 'nonbrand'
	and ws.created_at < '2012-11-27'
group by 1,2;

/*
4. I’m worried that one of our more pessimistic board members may be concerned about the large % of traffic from Gsearch. 
Can you pull monthly trends for Gsearch, alongside monthly trends for each of our other channels?
*/
-- first, find the various utm sources and refers to see the traffic we're getting
SELECT DISTINCT
	utm_source,
    utm_campaign,
    http_referer
FROM website_sessions ws
WHERE ws.created_at < '2012-11-27';

SELECT
	YEAR(ws.created_at) As yr,
	MONTH(ws.created_at) As mo,
	COUNT(DISTINCT CASE WHEN utm_source = 'gsearch' THEN ws.website_session_id ELSE NULL END) AS gsearch_paid_sessions,
	COUNT(DISTINCT CASE WHEN utm_source = 'bsearch' THEN ws.website_session_id ELSE NULL END) AS bsearch_paid_sessions,
	COUNT(DISTINCT CASE wHEN utm_source IS NULL AND http_referer IS NOT NULL THEN ws.website_session_id ELSE NULL END) AS organic_search_sessions,
	COUNT(DISTINCT CASE WHEN utm_source IS NULL AND http_referer IS NULL THEN ws.website_session_id ELSE NULL END) AS direct_type_in_sessions
FROM website_sessions ws
	LEFT JOIN orders
		ON orders.website_session_id = ws.website_session_id
WHERE ws.created_at < '2012-11-27'
GROUP BY 1,2;

/*
5. I’d like to tell the story of our website performance improvements over the course of the first 8 months. 
Could you pull session to order conversion rates, by month?
*/
SELECT 
	YEAR(website_sessions.created_at) AS yr,
	MONTH(website_sessions.created_at) AS mo,
	COUNT(DISTINCT orders.order_id) AS orders,
	COUNT(DISTINCT website_sessions.website_session_id) AS sessions,
    COUNT(DISTINCT orders.order_id)/COUNT(DISTINCT website_sessions.website_session_id) AS session_to_order_conv_rate
FROM website_sessions
	LEFT JOIN orders
		ON website_sessions.website_session_id = orders.website_session_id
WHERE website_sessions.created_at < '2012-11-27'
GROUP BY 1,2;

/*
6. For the gsearch lander test, please estimate the revenue that test earned us 
(Hint: Look at the increase in CVR from the test (Jun 19 – Jul 28), 
and use nonbrand sessions and revenue since then to calculate incremental value
*/
CREATE TEMPORARY TABLE nonbrand_lander_sessions
SELECT
	wp.website_session_id,
    MIN(wp.website_pageview_id) AS first_pv_id,
    wp.pageview_url AS landing_page
FROM website_pageviews wp
INNER JOIN website_sessions ws
	ON wp.website_session_id = ws.website_session_id
	AND ws.created_at < '2012-07-28'
    -- first lander 1 pageview
    AND wp.website_pageview_id >= (SELECT MIN(website_pageview_id) FROM website_pageviews WHERE pageview_url = '/lander-1')
    AND utm_source = 'gsearch'
    AND utm_campaign = 'nonbrand'
GROUP BY 1
HAVING landing_page IN ('/home','/lander-1');

SELECT
	landing_page,
    COUNT(DISTINCT website_session_id) AS sessions,
    COUNT(DISTINCT order_id) AS orders,
    COUNT(DISTINCT order_id)/COUNT(DISTINCT website_session_id) AS conv_rate
FROM(
SELECT 
	nls.website_session_id,
    nls.landing_page,
	od.order_id
FROM nonbrand_lander_sessions nls
	LEFT JOIN orders od USING(website_session_id)
) landers
GROUP BY 1;

-- /home cvr = .0318; /lader-1 cvr = .0406
-- additional orders per session: .0087

SELECT 
	COUNT(website_session_id) AS totalsessions_since_test
FROM website_sessions
WHERE created_at < '2012-11-27'
	AND website_session_id > 
		(SELECT 
			MAX(website_session_id) 
		 FROM website_sessions ws
         LEFT JOIN website_pageviews wp USING(website_session_id)
         WHERE wp.pageview_url = '/home'
			AND ws.utm_campaign = 'nonbrand'
            -- We should choose created_at from website_sessions table
            AND ws.created_at < '2012-11-27')
    AND utm_source = 'gsearch'
    AND utm_campaign = 'nonbrand';
 -- So we got 22,972 website sessions since the test-lander-1
 -- .0087*22,972 =~ 200 incremental orders since 7/29 to 11/27
 -- Roughly 4 months, 50 extra orders per month.
 
 /*
 7. For the landing page test you analyzed previously, 
 it would be great to show a full conversion funnel from each of the two pages to orders. 
 You can use the same time period you analyzed last time (Jun 19 – Jul 28） 
 */
WITH funnel AS(
SELECT
	website_pageviews.pageview_url,
    website_pageviews.website_session_id,
    website_pageviews.website_pageview_id,
	rank() over(partition by website_session_id order by website_pageview_id) AS ranking
FROM website_pageviews
INNER JOIN website_sessions
	ON website_sessions.website_session_id = website_pageviews.website_session_id
	AND website_sessions.created_at < '2012-07-28' 
    AND website_sessions.created_at > '2012-06-19'
    AND utm_source = 'gsearch'
    AND utm_campaign = 'nonbrand'
    ),
-- categarize pageviews into two landers '/home' and '/lander-1'
lander_funnel AS(
SELECT 
*
FROM funnel 
LEFT JOIN(
			SELECT
				pageview_url AS lander,
				website_session_id
			FROM funnel
			GROUP BY website_session_id) lander USING (website_session_id)
)

SELECT
	lander,
	ROUND((COUNT(DISTINCT CASE WHEN ranking = 2 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT website_session_id)) * 100, 2) AS lander_click_rt,
	ROUND(COUNT(DISTINCT CASE WHEN ranking = 3 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 2 THEN website_session_id ELSE NULL END) *100, 2) AS product_click_rt,
	ROUND(COUNT(DISTINCT CASE WHEN ranking = 4 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 3 THEN website_session_id ELSE NULL END) * 100, 2) AS mrfuzzy_click_rt,
	ROUND(COUNT(DISTINCT CASE WHEN ranking = 5 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 4 THEN website_session_id ELSE NULL END)* 100, 2) AS cart_click_rt,
	ROUND(COUNT(DISTINCT CASE WHEN ranking = 6 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 5 THEN website_session_id ELSE NULL END)* 100, 2) AS shipping_click_rt,
	ROUND(COUNT(DISTINCT CASE WHEN ranking = 7 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 6 THEN website_session_id ELSE NULL END) * 100, 2) AS billing_click_rt
FROM lander_funnel
GROUP BY lander;

/*
8. I’d love for you to quantify the impact of our billing test, as well. 
Please analyze the lift generated from the test (Sep 10 – Nov 10), in terms of revenue per billing page session, 
and then pull the number of billing page sessions for the past month to understand monthly impact
*/
SELECT 
	billing_version_seen,
    COUNT(DISTINCT website_session_id) AS sessions,
    SUM(price_usd)/COUNT(DISTINCT website_session_id) AS revenue_per_billing_page_seen
FROM(
SELECT
	website_pageviews.website_session_id,
	website_pageviews.pageview_url AS billing_version_seen,
	orders.order_id,
	orders.price_usd
FROM website_pageviews
LEFT JOIN orders
	ON orders.website_session_id = website_pageviews.website_session_id
WHERE website_pageviews.created_at > '2012-09-10' -- prescribed in assignment
	AND website_pageviews.created_at < '2012-11-10' -- prescribed in assignment
	AND website_pageviews. pageview_url IN ('/billing', '/billing-2')
) AS billing_pageviews_order
GROUP BY 1;

-- $22.83 revenue per billing page seen for the old versoion
-- $31.34 for the new version
-- LIFT: $8.51 per billing pageview  

SELECT
	COUNT(website_session_id) AS billing_sessions_past_month
FROM website_pageviews
WHERE website_pageviews.pageview_url IN ('/billing','/billing-2')
	AND created_at BETWEEN '2012-10-27' AND '2012-11-27' -- past month
-- 1,194 billing sessions past month
-- LIFT: $8.51 per billing session
-- VALUE OF BILLING TEST: ＄10,160 over the past month

-- IF we only choose sessions of billing-2 pages, the result is "how much did the test ALREADY generate"?
-- However the question being asked is "what is the monthly impact of this test? How much money will thos test make us going forward on a monthly basis?"
-- So we should include both billing page
```