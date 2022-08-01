#funnel_analysis #mysql 

[[45-Conversion Funnels]]


```python
-- Demo on Building Convers ion Funnels

-- BUSINESS CONTEXT
	we want to build a mini conversion funnel, from / lander-2 to /cart
	we want to know how many peop le reach each step, and also dropoff rates
	for simplicity of the demo, we' re Looking at / Lander-2 traffic only
	for simplicityr of the demo, we' re Lookign at customers who Like Mr Fuzzy only
```

##  STEP 1: select all pageviews for relevant sessions
## STEP 2: identify each relevant pageview as the specific funnel step
## STEP 3: create the session-Level convers ion funnel view
## STEP 4: agg regate the data to assess funnel performance

first I will show you all of the pageviews we care about
then,I will remove the comments from my flag co lumns one by one to show you what that looks like

![HQXTRR](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/HQXTRR.png)
![J0Mn5b](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/J0Mn5b.png)

-- next we will put the previous query inside a subquery (similar to temporary tab les)
-- We will group by website_session_ id, and take the MAX() of each of the flags
--   this MAX() becomes a made_it flag for that session, to show the session made it there

![isQo0C](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/isQo0C.png)

![jnmJtD](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/jnmJtD.png)

![gDp8ah](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/gDp8ah.png)

![xqJrWt](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/xqJrWt.png)

# code总结
## From teacher
```sql

SELECT
	website_session_id,
    MAX(products_page) AS product_made_it,
    MAX(mrfuzzy_page) AS mrfuzzy_made_it,
    MAX(cart_page) AS cart_made_it,
    MAX(shipping_page) AS shipping_made_it,
    MAX(billing_page) AS billing_made_it,
    MAX(thx_page) AS thx_made_it
FROM(
-- 找到以lander-1为start的sessions的所有对应pageviews，url和created time
SELECT 
	fpl.website_session_id,
	wp.pageview_url,
	wp.created_at as pageview_created_at,
    CASE WHEN wp.pageview_url = '/products' THEN 1 ELSE 0 END AS products_page,
    CASE WHEN wp.pageview_url = '/the-original-mr-fuzzy' THEN 1 ELSE 0 END AS mrfuzzy_page,
	CASE WHEN wp.pageview_url = '/cart' THEN 1 ELSE 0 END AS cart_page,
    CASE WHEN wp.pageview_url = '/shipping' THEN 1 ELSE 0 END AS shipping_page,
    CASE WHEN wp.pageview_url = '/billing' THEN 1 ELSE 0 END AS billing_page,
    CASE WHEN wp.pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END AS thx_page
FROM first_pageviews_per_session_lander1 fpl
LEFT JOIN website_pageviews wp
	-- 根据session id合并table，这样把session的所有page都pull out
	ON fpl.website_session_id = wp.website_session_id

-- 每个session按照pageview的created at时间来排序，并只选择经理要求分析的网页
-- WHERE wp.pageview_url IN ('/lander-1','/products','/the-original-mr-fuzzy','/cart','/shipping','/billing','/thank-you-for-your-order')
ORDER BY
	fpl.website_session_id,
    wp.created_at
）AS pageview_level
```

## From students
```sql
SELECT

ROUND((COUNT(DISTINCT CASE WHEN ranking = 2 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT website_session_id)) * 100, 2) AS lander_click_rt,

ROUND(COUNT(DISTINCT CASE WHEN ranking = 3 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 2 THEN website_session_id ELSE NULL END) *100, 2) AS product_click_rt,

ROUND(COUNT(DISTINCT CASE WHEN ranking = 4 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 3 THEN website_session_id ELSE NULL END) * 100, 2) AS mrfuzzy_click_rt,

ROUND(COUNT(DISTINCT CASE WHEN ranking = 5 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 4 THEN website_session_id ELSE NULL END)* 100, 2) AS cart_click_rt,

ROUND(COUNT(DISTINCT CASE WHEN ranking = 6 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 5 THEN website_session_id ELSE NULL END)* 100, 2) AS shipping_click_rt,

ROUND(COUNT(DISTINCT CASE WHEN ranking = 7 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN ranking = 6 THEN website_session_id ELSE NULL END) * 100, 2) AS billing_click_rt

  

FROM(

SELECT

    website_pageviews.pageview_url,

    website_pageviews.website_session_id,

    website_pageviews.website_pageview_id,

    #COUNT(p.website_session_id),

    rank() over(partition by website_session_id order by website_pageview_id) AS ranking

FROM website_pageviews

INNER JOIN website_sessions

ON website_sessions.website_session_id = website_pageviews.website_session_id

    AND website_sessions.created_at BETWEEN '2012-08-05' AND '2012-09-05'

    #AND website_sessions.created_at <'2012-09-05'

    AND utm_source = 'gsearch'

    AND utm_campaign = 'nonbrand'

WHERE website_pageviews.pageview_url IN ('/lander-1', '/products', '/the-original-mr-fuzzy', '/cart', '/shipping', '/billing', '/thank-you-for-your-order' )

) AS funnel;
```
