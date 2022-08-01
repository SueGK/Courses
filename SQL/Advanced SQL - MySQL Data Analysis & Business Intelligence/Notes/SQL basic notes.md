# SQL with Mosh

![KYF4lb3Ge2](https://i.loli.net/2020/06/29/BhlxA21DLinHZbe.png)

---

## 三、多张表中检索数据

### 1. 内连接 - (Inner) Joins

<img src="https://i.loli.net/2020/06/11/jFpKVTbX9ek1CUu.png" alt="image-20200603214854371" style="zoom:67%;" />

---

### 2. 跨数据库连接 - Joining Across Databases

⚡需要给不在当前数据库的表加前缀

<img src="https://i.loli.net/2020/06/11/5tphoWITsiE4gNm.png" alt="image-20200603215148218" style="zoom:80%;" />

---

### 3. 自连接 - Self Joins

```mysql
USE sql_hr;

SELECT 
	#获得员工id，员工名，对应管理人员名
	e.employee_id,
	e.first_name,
	m.first_name AS manager
FROM employees e
JOIN employees m #management找到管理人员
	ON e.reports_to = m.employee_id
```

---

### 4. 多表连接 - Joining Multiple Tables

<img src="https://i.loli.net/2020/06/11/j6dUxgGzaiWbR52.png" alt="image-20200603220650241" style="zoom:80%;" />



---

### 5. 复合连接条件 - Compound Join Conditions 

之前都是用单一列唯一识别某张表的行，当有复合主键时![image-20200603220909254](https://i.loli.net/2020/06/11/iJq2b6TSRgQD4Z5.png)

<img src="https://i.loli.net/2020/06/11/PVQ42IeS1ouAqpB.png" alt="image-20200603221450167" style="zoom: 80%;" />



---

### 6. 隐含连接语法 - Implicit Join Syntax 

**Not commend**

![image-20200603221602618](https://i.loli.net/2020/06/11/pSdQUiz7R3mBMwP.png)

---

### 7. 外连接 Outer Joins

内连接缺点：只有满足on条件的record才会被返回，然而有些顾客没有订单，为null，无法返回![image-20200602192123489](https://i.loli.net/2020/06/11/gHvTl9i7dbYr8uP.png)

![image-20200602192201914](https://i.loli.net/2020/06/11/V7nhMiC8uSIy4rR.png)

如何返回所有的record？——外连接

**LEFT JOIN**

所有**左表**的record都会被返回，与后面条件true or not无关。同理RIGHT JOIN返回右表!

![image-20200602191349331](https://i.loli.net/2020/06/11/1amNOK9dTI7LFkc.png)

![image-20200602201241751](https://i.loli.net/2020/06/11/MtFGY7riEqRKkbn.png)



```
总结：
JOIN: 内连接
LEFT/RIGHT JOIN：外连接【最好使用左连接】
```

---

### 8. 多表外连接

练习图表：

orders![image-20200602211305945](https://i.loli.net/2020/06/11/f89t5ldyJGFYOuq.png)

order_statuses![image-20200602212021782](https://i.loli.net/2020/06/11/w78JFNsjEQ4ynBt.png)

order_shippers![image-20200602214011262](https://i.loli.net/2020/06/11/38L7nb6sut5IXCg.png)

结果：![image-20200602211012492](https://i.loli.net/2020/06/11/EC8LT2KWwgYub3N.png)

![image-20200602210922080](https://i.loli.net/2020/06/11/SKU89CZNQx5uXkP.png)

---

### 9. 自我外部连接 Self Outer Joins

employees表自己连接自己，命名一个为e，一个为m![image-20200602214520214](https://i.loli.net/2020/06/11/xofSbJ9jzqAeZuM.png)



---

### 10. USING子句- The USING Clause (5:22)

<span style ='color:purple;font-weight:bold'>USING只能在不同表中列名完全一样的情况下使用</span>

单个主键：

![23](C:\Users\gsk07\Desktop\23.png)

多个主键：![image-20200603192435362](https://i.loli.net/2020/06/11/5nxJAs73TrvCmuF.png)



---

### 11. 自然连接 Natural Joins

**Not Commend**

连接两个表的快捷方式: 数据库引擎会基于共同的列自动连接

```mysql
SELECT 
	o.order_id,
	c.name
FROM orders o
NATURAL JOIN customers c
```

---

### 12. 交叉连接 Cross Joins

> 结合或者连接第一个表的每条记录和第二个表的每条记录
>
> 🎈应用：有一个型号表如小中大，一个颜色表红绿蓝，想要将所有型号和颜色组合

* 显式语法(explict syntax)：

  ```mysql
  SELECT 
  	c.first_name AS customer,
  	p.name AS product
  FROM Customers c
  CROSS JOIN products p
  ORDER BY c.first_name
  ```

  

* 隐式语法(implicit syntax)：【**Simple**】

  ```mysql
  SELECT 
  	c.first_name AS customer,
  	p.name AS product
  FROM Customers c, products p ✨
  ORDER BY c.first_name
  ```

---

### 13.  联合 - Unions 

> 结合多张表的**行**
>
> 相同表格：通过UNION可以合并多段的查询record
>
> 不同表格：将结果合并到一个结果集

```mysql
#返回2019年正在进行的订单
SELECT
    order_id,
    order_date
	'Active' AS status #新建一列命名为Active
FROM orders
WHERE order_data >= '2019-01-01'

#将上下分别查询得到的record结合起来，新的结果集列名以第一段SELECT后的名字决定
UNION 

#返回2019年之前归档的订单 
SELECT
    order_id,
    order_date
	'Actived' AS status #新建一列命名为Archived
FROM orders
WHERE order_data < '2019-01-01'
```



⚡第一段查询SELECT后的名字被用来当作UNION后的列名<img src="https://i.loli.net/2020/06/11/y4O5hFGnxumCsb9.png" alt="image-20200603211017047" style="zoom:80%;" />



⚡查询返回的列数量一定要相同：错误案例如下-一个选两行，一个选一行<img src="https://i.loli.net/2020/06/11/o8P5anGckvysjFu.png" alt="image-20200603210030074" style="zoom:80%;" />

---

## 四、插入、更新和删除数据

### 1. 列属性 - Column Attributes 

VARCHAR(50): 如果该列名字只需要输入5个字符，它会只储存5个字符

```MYSQL
⚡#一般使用VARCHAR来储存字符串或者文本值
```

CHAR(50): 如果该列名字只需要输入5个字符，它会再插入45个空格符填满这列

NN: Not null 不可是空值

AI：自动递增，常用在主键

---

### 2. 插入单行 - Inserting a Row

<img src="https://i.loli.net/2020/06/11/B91AVofUlWMbaN8.png" alt="image-20200604111420333" style="zoom:120%;" />

![image-20200604111050425](https://i.loli.net/2020/06/11/obdyhQfixSZKG7s.png)

<span style='color:red;font-weight:bold'>1.  输入插入新record的所有列值</span>

```mysql
#插入单行
INSERT INTO customers
#用默认关键字，让sql自动生成一个顾客id的唯一值，即AI
VALUES (
    DEFAULT,
    'Join',
    'Smith',
    '1990-01-01',
    NULL,
    'address',
    'city',
    'CA',
    300/DEFAULT)
```

<span style='color:red;font-weight:bold'>2. 只输入选定列的值</span>

```mysql
#在表的名称后面明确想要赋值的列名称
INSERT INTO customers(
    first_name,
    last_name,
    birth_date,
    address,
    city,
    state)
#只需要提供上面想要插入列的对应值
VALUES (
    'Join',
    'Smith',
    '1990-01-01',
    'address',
    'city',
    'CA')
⚡#可以同时改变列和列对应值的顺序输入，如颠倒state city和CA city
```

---

### 3. 插入多行 - Inserting Multiple Rows 

```mysql
INSERT INTO shippers (name,stock,price)
VALUES  ('shipper1',10,1.95),
		('shipper2',11,1.87),
		('shipper3',12,2.54)
```

---

### 4. 插入分层行 - Inserting Hierarchical Rows 

> 一笔order可以有多个order_items，即’亲子关系‘
>
> 订单表为母方，订单项目表为子方 

**orders**![image-20200604113559485](https://i.loli.net/2020/06/11/R7GxEI3chOnPDwj.png)

**order_items**![image-20200604113959427](https://i.loli.net/2020/06/11/yBbtK76WHxq8ZVg.png)



```mysql
INSERT INTO orders (customer_id, order_date, status)
VALUES (1,'2019-01-01',1);

#Function-LAST_INSERT_ID(): 返回我们插入新行时mysql自动生成的id
INSERT INTO order_items
VALUES (LAST_INSERT_ID(),1,1,2.95),
	   (LAST_INSERT_ID(),2,1,3.95)
```

---

### 5. 创建表复制 - Creating a Copy of a Table 

>  💡 快速将数据从一张表复制到另一张，而不用INSERT INTO输入所有行

```mysql
#创建新表
CREATE TABLE order_archived AS
SELECT * FROM orders
#⚡此时新表没有注明PK主键和AI自动递增，需要我们自己输入

#选中order_archived，右键Truncate Table，删除表中的所有数据，返回query「此时为空」
只复制订单表的一部分record到这张表，如2019年前,输入下方代码后回表里刷新数据

INSERT INTO order_archived
SELECT *
FROM oders
WHERE order_date < '2019-01-01'
```

> 📌 sql_invoicing数据库 复制支付过的发票，即只选择有付款日期的发票

```mysql
#创建新表
CREATE TABLE invoice_archived AS
SELECT 
	i.invoice_id,
	c.name, 
    i.number, 
    i.invoice_total,
    i.invoice_date, 
    i.payment_date
FROM invoices i
JOIN clients c 
	USING(client_id)
#📍注意payment_date前不需要加新建的数据库名 如invoice_archived.pay…；
#此时的where使用的就是前面JOIN后的整体数据
WHERE payment_date IS NOT NULL
```

---

### 6. 更新单行 - Updating a Single Row 

> 💡 使用UPDATE语句更新表中的一条或多条记录，SET子句指定一列或多列的新值

![image-20200604164754161](https://i.loli.net/2020/06/11/MAGWXfJbrl31dCx.png)

![image-20200604170654526](https://i.loli.net/2020/06/11/epfmES4Rl5Z2j1n.png)

```mysql
--UPDATE
  SET abc=x
  WHERE 主键=xx

#更新1：
UPDATE invoices
SET payment_total =10, payment_date = '2019-03-01'
WHERE invoice_id=1

#更新2：DEFAULT会使sql在该列填充默认值，即如图所示，0
UPDATE invoices
SET payment_total =DEFAULT, payment_date = NULL
WHERE invoice_id=2

#更新3：顾客在截止日期付款了发票总额的50%
UPDATE invoices
SET 
    payment_total = invoice_total*0.5, 
    payment_date = due_date
WHERE invoice_id=3
```

---

### 7. 更新多行 - Updating Multiple Rows 

> ⚡默认情况下，mysql在安全模式，只允许更新一条记录，其他mysql可。sqlworkbench不可，需选择Edit→SQL Editor→Safe Updates关闭

![image-20200604172946143](https://i.loli.net/2020/06/11/N6m2WaoMJ84Zl5q.png)

```mysql
#client_id=3的有三条record，同时更新
UPDATE invoices
SET 
    payment_total = invoice_total*0.5, 
    payment_date = due_date
WHERE client_id=3
#同时更新客户3和客户4
——WHERE client_id IN (3,4)
#同时更新全表，不需要写where
```

---

### 9. 删除行 - Deleting Rows 

> 📌如果只有想要更新数据的客户名字而非id怎么办？

```mysql
UPDATE invoices
SET 
    payment_total = invoice_total*0.5, 
    payment_date = due_date

#更新1：更新客户'kimseokjin'的所有record
#Mysql会先执行括号内的子查询，返回client_id，再把它用于WHERE的条件  
WHERE client_id=
             (SELECT client_id 
             FROM clients
             WHERE name = 'Kimseokjin')
 
#更新2：更新纽约或洛杉矶客户的record
WHERE client_id=
             (SELECT client_id 
             FROM clients
             WHERE state IN ('CA','NY'))

💡最好在执行UPDATE语句前先选中括号里的内容执行查询，看得出的结果是否是自己想要的！

UPDATE orders
SET comments = 'gold customer'
WHERE customer_id IN #因为有多位顾客ID，所以用IN运算符；单个用=
			(SELECT customer_id 
             FROM customers
             WHERE points > 3000)

```

---

### 9. 删除行 - Deleting Rows 

```mysql
#DELETE FROM 删除整张表的record
DELETE FROM invoices
#WHERE选择具体要删除的行
WHERE client_id=(
SELECT * FROM clients
WHERE name = 'myworks')
```

---

### 10. 恢复数据库 - Restoring the Databases

> 💡 file-SQL script-你储存SQL脚本的目录位置-打开create-databases-运行-刷新左栏Navigator

---

## 五、汇总数据

### 1. 聚合函数 - Aggregate Functions

> 💡 如何按照客户或州来报告销量，可以看不同州卖了多少

#### MAX/AVG/COUNT()
```mysql
MAX()
MIN()
AVG()
SUM()
COUNT()

SELECT 
	MAX(payment_date) AS highest,
	MIN(invoice_total*1.1 ) AS lowest,
	
#count函数返回非空payment_date的记录数，如共16个记录有一个为null，则count结果为15
COUNT(payment_date) AS count_of_payments,

#如果想要得到表格中的所有记录条目，不管是不是空值，用*
COUNT(*) AS total_records

#客户id可能会有重复，如客户1购买多次，有多条record，如果想统计客户数
COUNT(DISTINCT client_id) AS total_records

FROM invoices

```

⚡**Exercise：**

**invoices表格：**![image-20200612213148506](https://i.loli.net/2020/06/12/adlngcpUGKqMxLu.png)

**？得到如下表格：**![image-20200612215126570](https://i.loli.net/2020/06/12/X3DmiUBMgV49oHT.png)

```mysql
💡SOLUTION:

#首先计算2019年上半年的所有值
SELECT 
	'First half of 2019' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments，
    SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-01-01' AND '2019-06-30'

#联合2019年下半年的所有值
UNION
SELECT 
	'Second half of 2019' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments，
    SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-07-01' AND '2019-12-31'

#联合2019年下半年的所有值
UNION
SELECT 
	'Total' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments，
    SUM(invoice_total-payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-01-01' AND '2019-12-31'
```

---

### 2. GROUP BY子句 - The GROUP BY Clause

<span style='color:red;font-weight:bold'>根据单列分组：</span>

```mysql
SELECT
	client_id
	SUM(invoice total) AS total_sales
FROM invoices
-- WHERE invoice_date >= '2019-07-01'
GROUP BY client_id
ORDER BY total_sales DESC
#假设你想对每个客户计算总销售额，但仅看上半年——加WHERE在GROUP BY前
```

<span style='color:red;font-weight:bold'>根据多列分组：</span>

```mysql
#根据多列来分组，如下根据的是JOIN的表clients中的state，city来分组
SELECT
	state,
	city,
	SUM(invoice total) AS total_sales
FROM invoices i
JOIN clients USING(client_id)
GROUP BY state,city
```

![image-20200612220459310](https://i.loli.net/2020/06/12/3fVuGBlqi8omLxR.png)

---

⚡**Exercise:**

payments：

![image-20200612222305961](https://i.loli.net/2020/06/12/8JzUclIxBjgWSAZ.png)

payment_method:

![image-20200612222333168](https://i.loli.net/2020/06/12/5knucDO4KRzioW7.png)

要求得到如下结果：![image-20200612222444683](https://i.loli.net/2020/06/12/LbIXCBHFSaOA2fr.png)

💡**Solution:**

```mysql
SELECT 
	date,
    pm.name AS payment_method,
    SUM(amount) AS total_payments
FROM payments p
JOIN payment_methods pm 
	ON p.payment_method= pm.payment_method_id
GROUP BY date,payment_method 
ORDER BY date
```

---

### 3. HAVING子句 - The HAVING Clause

> 🔔分组后的数据如何如何再筛选？如total_payents>50.
>
> 不能用WHERE语句，因为WHERE要在GROUP BY语句前面，只能在分组前筛选

<span style='color:red;font-weight:bold'>HAVING语句在分组行之后筛选数据</span> 

<span style='color:red;font-weight:bold'>WHERE语句在分组行之前筛选数据</span> 

**HAVING语句中用到的列一定是SELECT子句中存在的；**

**但WHERE语句可以用任何列，不管SELECT有没有选中**

```mysql
SELECT
	client_id,
	SUM(invoice_total) AS total_sales，
	COUNT(*) AS number_of_invoices
FROM invoices
GROUP BY client_id
HAVING total_sales > 500 AND number_of_invoices >5
#HAVING语句中用到的列一定是SELECT子句中存在的，即必须为client_id、total_sales、total_sales中的；但WHERE语句可以用任何列，不管SELECT有没有选中
```

---

⚡**Exercise:**

``` 
GET the customers
	located in VA
	who have spent more than $100
```

customers:![image-20200613211959742](https://i.loli.net/2020/06/13/NAK2hsIoErGQRSf.png)

orders:

![image-20200613212029087](https://i.loli.net/2020/06/13/jRJiHdYCeWXsNwD.png)

order_items:

![image-20200613212047801](https://i.loli.net/2020/06/13/QfngjyOcNipwhWX.png)



💡**Solution:**

```mysql
SELECT 
	c.customer_id,
    c.first_name,
    c.last_name,
    SUM(oi.quantity * oi.unit_price) AS total_sales
FROM customers c
JOIN orders o USING(customer_id)
JOIN order_items oi USING(order_id)
WHERE state = 'VA' #分组前用WHERE筛选数据
GROUP BY #根据SELECT栏选择的分类，聚合函数除外
	c.customer_id,
    c.first_name,
    c.last_name
HAVING total_sales > 100 #分组后用HAVING筛选数据
```

---

### 4. WITH ROLLUP - The ROLLUP Operator 

```mysql
SELECT
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
GROUP BY client_id
```

**结果：**

![image-20200613212729919](https://i.loli.net/2020/06/13/5e1OF28bJNmE3ua.png)

```mysql
SELECT
	client_id,
	SUM(invoice_total) AS total_sales
FROM invoices
GROUP BY client_id WITH ROLLUP 
#多了一行汇总结果
#ROLLUP operator只能应用于「聚合」值
```

**结果：**

![image-20200613212815103](https://i.loli.net/2020/06/13/f7Qu62tHCpXg5UZ.png)

```mysql
💡课后练习：

SELECT 
	pm.name AS payment_method,
    SUM(amount) AS total
FROM payments p
JOIN payment_methods pm ON p.payment_method = pm.payment_method_id
-- ☒GROUP BY [payment_method] WITH ROLLUP
GROUP BY pm.name WITH ROLLUP
#注意要用pm.name而不是AS后的名字
```

---

##  六、编写复杂查询
![5RHXDL](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/5RHXDL.png)

### 1.子查询 - Subqueries

```mysql
⚡问题：
#In sql_hr database: Find employees whose earn more than average

USE sql_hr;

SELECT *
FROM employees
WHERE salary > (
	SELECT AVG(salary) 
    FROM employees
)
```

---

### 2. IN运算符 - The IN Operator 

> 💡查找没有被订购过的产品？

在order_items表中，我们得知每个产品被订购的次数![image-20200613215843155](https://i.loli.net/2020/06/13/HbpYreXniscONz7.png)

我们首先找到表内所有产品，再写另一端查询来返回不在这张表里的产品

![image-20200613220325301](https://i.loli.net/2020/06/13/c2YOHiyDMmhrJQT.png)

```mysql
#先写括号里的，再写括号外的查询
USE sql_store;
SELECT *
FROM products
WHERE product_id NOT IN(
SELECT DISTINCT product_id
FROM order_items
)
```

---

### 3. Subqueries vs Joins [*](https://mode.com/sql-tutorial/sql-sub-queries/#joining-subqueries)
You may remember that you can [filter queries in joins](https://mode.com/sql-tutorial/sql-joins-where-vs-on). 
**It's fairly common to join a subquery that hits the same table as the outer query rather than filtering in the `WHERE` clause.** 
__This can be particularly useful when combined with aggregations.__ 
<span style='color:purple;font-weight:bold'>When you join, the requirements for your subquery output aren't as stringent as when you use the `WHERE` clause. For example, your inner query can output multiple results.</span>
The following query ranks all of the results according to how many incidents were reported in a given day. It does this by aggregating the total number of incidents each day in the inner query, then using those values to sort the outer query:
![nc7xTj](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/nc7xTj.png)

> 💡查找没有发票的客户-方法2

```mysql
#方法1：子查询
SELECT *
FROM clients
WHERE client_id NOT IN(
SELECT DISTINCT client_id 
FROM invoices
)

#方法2：连接
SELECT *
FROM clients
LEFT JOIN invoices USING(client_id)
WHERE invoice_id IS NULL

#根据性能or易读性决定使用哪种方法
```

---

⚡**Exercise:**

![image-20200613233208258](https://i.loli.net/2020/06/13/GPWkHlQ92bmDnsR.png)

💡**Solution:**

<span style='color:red;font-weight:bold'>方法1:SELECT</span>

```mysql
SELECT *
FROM customers c #想要找什么就选中哪个表 该题要找顾客，就选中顾客表
WHERE customer_id IN(  #在子查询中需要返回订购了生菜的顾客id
	SELECT *
	FROM order_items 
	WHERE product_id = 3 
)#在这张表中没有顾客id，所以要连接到orders表，来获得顾客id；如下

SELECT 
	customer_id,
	first_name,
	last_name
FROM customers c 
WHERE customer_id IN(  
	SELECT o.customer_id
	FROM order_items oi
	JOIN orders o USING(order_id)
	WHERE product_id = 3
)
```

结果：![image-20200613233952628](https://i.loli.net/2020/06/13/o8YMZitLJkyl69G.png)

<span style='color:red;font-weight:bold'>方法2：JOIN—更易读</span> 

```mysql
SELECT *
FROM customers c
JOIN orders o USING(customer_id)
JOIN order_items oi USING(order_id)
WHERE product_id = 3
```

结果：![image-20200613234132876](https://i.loli.net/2020/06/13/HklE9By4sSW5pcO.png)

```mysql
#上面结果顾客2出现了两次，所以我们SELECT使用DISTINCT
SELECT 
	DISTINCT customer_id,
	first_name,
	last_name
FROM customers c
JOIN orders o USING(customer_id)
JOIN order_items oi USING(order_id)
WHERE product_id = 3
```

---

### 4. ALL关键字 - The ALL Keyword

> ⚡选择大于客户3 所有发票额的发票： 大于所有值即大于最大值

#### <span style='color:red;font-weight:bold'>不使用ALL,用MAX聚合函数：</span>

```mysql
#首先选中客户3的all invoices
SELECT *
FROM invoices
WHERE client_id = 3

#稍微修改一下，选中3的最大额发票值
SELECT MAX(invoice_total)
FROM invoices
WHERE client_id = 3

#将其作为子查询，查询大于3最大值的发票
SELECT *
FROM invoices
WHERE invoice_total >(
    SELECT MAX(invoice_total)
    FROM invoices
    WHERE client_id = 3
)
```

#### <span style='color:red;font-weight:bold'>使用ALL：</span>

```mysql
SELECT *
FROM invoices
WHERE invoice_total > ALL(150,130,...)
#mysql会查看invoices表,会把每行的发票总额和括号里的数字比较，如果发票总额大于所有括号里的值，那么那行就会返回在最终结果集

SELECT *
FROM invoices
WHERE invoice_total > ALL(
    SELECT invoice_total
    FROM invoices
    WHERE client_id = 3
)
```

```mysql
子查询返回：
	单一值 #MAX函数
	一列值 #ALL keyword
	一张表
```

---

### 5. = ANY - The ANY Keyword

> 💡选择至少有两张发票的客户

#### <span style='color:red;font-weight:bold'>不使用ANY,用IN运算符：</span>

```mysql
#首先知道每位客户的发票数
SELECT client_id
FROM invoices
GROUP BY client_id
HAVING COUNT(*) >= 2 #分组后筛选拥有大于2张发票的客户id

#我们最后想要知道的是具体客户
SELECT *
FROM clients
WHERE client_id IN(
    SELECT client_id
    FROM invoices
    GROUP BY client_id
    HAVING COUNT(*) >= 2
)
```

#### <span style='color:red;font-weight:bold'>用= ANY：</span>

```mysql
SELECT *
FROM clients
WHERE client_id = ANY( 
    #客户id=这段查询返回值里的任意一个，该客户返回到最终结果
    SELECT client_id
    FROM invoices
    GROUP BY client_id
    HAVING COUNT(*) >= 2
)
```

---

### 6. 相关子查询 - Correlated Subqueries 

> ⚡Select employees whose salary is above the average in their office
>
> for each employee
> 	calculate the avg salary for employee. office
> 	return the employee if salary > avg

```mysql
SELECT *
FROM employees e
WHERE salary > (
	SELECT AVG(salary)
    FROM employees
    WHERE office_id = e.office_id
)
```

---

### 7. EXISTS运算符 - The EXISTS Operator

> ⚡select clients who have an invoice 筛选所有有发票的顾客

```mysql
#方法1：子查询
SELECT *
FROM clients
WHERE client_id IN(
SELECT DISTINCT client_id 
FROM invoices
)

#方法2：连接
SELECT *
FROM clients
LEFT JOIN invoices USING(client_id)
WHERE invoice_id IS NOT NULL

#方法3：EXISTS
#如果是张非常大的表，方法1相当于 WHERE..IN(1,2,........)，即IN运算符后写的子查询生成了很大的结果集影响性能，使用EXIST运算符可以提高效率
子查询并没有给外查询返回一个结果，它会返回一个指令，说明这个子查询中是否有符合这个搜索条件的行：
我们发票表里有没有这个id的客户？
只要找到表中有一条匹配，它就会返回TRUE给EXISTS运算符；
然后这个EXISTS运算符就会在最终结果里添加当前记录，也就是当前客户。

SELECT *
FROM clients c
WHERE EXISTS(
	SELECT client_id
    FROM invoices
    WHERE client_id = c.client_id
)

#查找没有发票的客户时，直接加NOT：WHERE NOT EXISTS()
```

---

### 8. SELECT子句的子查询 - Subqueries in the SELECT Clause

> 💡 子查询不单单在WHERE子句中使用，还可以用在选择子句以及FROM子句

==聚合函数只返回一个值，除非再分组==

```mysql
SELECT 
	invoice_id,
    invoice_total,
    AVG(invoice_total) AS invoice_average
FROM  invoices
```

结果：![image-20200614151337569](https://i.loli.net/2020/06/14/syGFgtB1Cr3TMaI.png)

==SELECT子句的子查询，可返回一列值==

```mysql
SELECT 
	invoice_id,
    invoice_total,
    (SELECT AVG(invoice_total) FROM invoices) AS invoice_average,
    invoice_total - (SELECT invoice_average) AS invoice_average
                    #表达式中不能使用列的别名 
FROM  invoices
```

结果：![image-20200614152346363](https://i.loli.net/2020/06/14/HaULJFnf5EgDiPK.png)

---

⚡**Exercise:** 写一段查询，生成下图结果

💡**Solution:**

```mysql
#我的答案：
SELECT 
	client_id,
	name,
    (SELECT SUM(invoice_total) FROM invoices
    WHERE client_id = c.client_id) AS total_sales,
    (SELECT AVG(invoice_total) FROM invoices) AS average,
    (SELECT total_sales - average) AS difference
    #子查询里可以直接减，不用(SELECT total_sales) - (SELECT average),麻烦
FROM clients c
LEFT JOIN invoices i USING(client_id)
GROUP BY client_id

#答案更正：
	去掉JOIN和GROUP BY，子查询里有FROM invoices，所以不需要连接；
	子查询里直接减
	
SELECT 
	client_id,
	name,
    (SELECT SUM(invoice_total) FROM invoices
    WHERE client_id = c.client_id) AS total_sales,
    (SELECT AVG(invoice_total) FROM invoices) AS average,
    (SELECT total_sales - average) AS difference
FROM clients c
```

![image-20200614154038088](https://i.loli.net/2020/06/14/t6wp2dmEgqJSiBy.png)

---

### 9. FROM子句的子查询 - Subqueries in the FROM Clause

```
每当我们在FROM子句中使用子查询，我们必须给子查询一个别名。
在select语句的form子句中写子查询，会让我们的主查询变得更复杂。
	1.仅限于简单的查询
	2.一个更好的解决方法就是使用视图，所以我们可以使用这段查询作为视图储存在数据库中。然后我们可以把这个视图取名为销售信息汇总。
```

<span style='color:red;font-weight:bold'> 虚拟表格里的数据如何作为数据库中的真是表格使用：</span>

```mysql
SELECT *
FROM (
	SELECT 
		client_id,
		name,
		(SELECT SUM(invoice_total) FROM invoices
		WHERE client_id = c.client_id) AS total_sales,
		(SELECT AVG(invoice_total) FROM invoices) AS average,
		(SELECT total_sales - average) AS difference
	FROM clients c
    ) AS sales_summary
#看上去和之前结果一样，但我们可以筛选数据、把这张表和别的表连接、分组数据....
WHERE total_sales IS NOT NULL #筛选不含空值的
```

---

## 七、MySQL的基本函数 - Essential MySQL Functions

### 1. 数值函数 - Numeric Functions

```mysql
#ROUND：四舍五入，保留几位小数
SELECT ROUND(5.7345,2) ——→5.73

#TRUNCATE: 截断数字，保留几位小数
SELECT TRUNCATE(5.7345,2) ——→5.73

#CEING：返回大于或等于这个数字的最小整数
SELECT CEING(5.7) --→6

#FLOOR: 返回小于或等于这个数字的最大整数
SELECT FLOOR(5.2) --→5

#ABS：绝对值

#RAND：生成0~1区间的随机浮点数

#其他：google mysql numeric functions
```

---

### 2. 字符串函数 - String Functions

```mysql
#LENGTH: 得到字符串中的字符数
SELECT LENGTH('SKY') --→3

#UPPER/LOWER：将字符串转化成大写或小写字母
SELECT UPPER('sky') --→SKY

#删除字符串中不需要的空格：处理用户输入数据时极为有用
#LTRIM：移除字符左侧的空自字符或其他预定义字符，LEFT TRIM简写
#RTRIM：移除字符右侧的空自字符或其他预定义字符
#TRIM：移除所有空格

#LEFT:返回字符串左边几个字符
SELECT LEFT('kindergarten'，4) --→kind
#RIGHT:返回右边

#SUBSTR：字符截取函数，得到一字符串任何位置的字符，SUBSTRING缩写
	#SUBSTR('字符串'，起始位置，长度)
SELECT SUBSTR('kindergarten'，3，5) --→nderg

#LOCATE：返回第一个字符或一串字符匹配位置
	#LOCATE('要搜索的字符串'，'字符串')
SELECT LOCATE('n','kindergarten') --→3 n位置为3
SELECT LOCATE('N','kindergarten') --→3 不区分大小写
SELECT LOCATE('q','kindergarten') --→0 没有的返回0
SELECT LOCATE('gar','kindergarten') --→7 第一次出现位置

#REPLACE：替换一个字符或一串字符
	#SELECT LOCATE('字符串','被替换的','替换的') 
SELECT LOCATE('kindergarten'，'garten','garden') --→kindergarden

#CONCAT：串联两个字符串
SELECT CONCAT('FIRST','LAST') --→FIRSTLAST
EG：
SELECT CONCAT('first_name',' ','last_name') AS full_name
```

---

### 3. MySQL中的日期函数 - Date Functions #sql/dates 
![rVHoNG](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/rVHoNG.png)

```mysql
#NOW: 调用当前日期和时间
#CURDATE: 当前日期（current date）
#CURTIME: 当前时间（current time）
```

![image-20200614231605320](https://i.loli.net/2020/06/14/fav3RX1D9KHcYBM.png)



```mysql
#提取特定日期或时间的构成元素
#YEAR：提取年份/月/日...均为整数,数字
SELECT 
	YEAR(NOW()),
	MONTH(NOW()),
	HOUR(NOW()),
    MINUTE(now()),
    SECOND(now())
```

![image-20200614232506638](https://i.loli.net/2020/06/14/ZCh7PUzXrYt9axy.png)

```mysql
#提取特定日期的字符串
SELECT 
	DAYNAME(NOW()),
	MONTHNAME(NOW()),
	EXTRACT(DAY FROM NOW()), #EXTRACE(想要获取的单位 FROM关键字 时间日期值)
	EXTRACT(YEAR FROM NOW())
```

![image-20200614233012931](https://i.loli.net/2020/06/14/WsRpoy67hgmTAl9.png)

---

⚡**Exercise:** 修改代码，查询确切在当前年下的订单，不必每过一年改一次年数

💡 **Solution:**

```mysql
假设当前年份为2019，等明年需要改成2020，麻烦
SELECT *
FROM orders
WHERE order_date >= '2019-01-01'

更改后：
SELECT *
FROM orders
WHERE YEAR(order_date) =YEAR(NOW())
```

---

### 4. 格式化日期和时间 - Formatting Dates and Times 

```mysql
#两个参数，一个日期值和一个格式字符串
DATE_FORMAT(NOW(),'')
```

**==%y:==** 两位的年份 19

**==%Y:==** 四位的年份 2019

**==%y:==** 数字的月份 03

**==%Y:==** 英文的月份 March

**==%d:==** 数字的日期 27

**==%D:==** 英文的日期 27th

![image-20200627191238504](https://i.loli.net/2020/06/27/vQp7REDT3aGdNZM.png)

```mysql
#两个参数，一个日期值和一个格式字符串
TIME_FORMAT(NOW(),'')

SELECT 
	TIME_FORMAT(NOW(), '%r' ) AS DATE,
	TIME_FORMAT(NOW(), '%T' ) AS DATE2,
	TIME_FORMAT(NOW(), "%h:%i:%s %p") AS DATE3,
    TIME_FORMAT(NOW(), "%H:%i:%s %p") AS DATE4,
    TIME_FORMAT(NOW(), "%H:%i %p") AS DATE5
```

![image-20200627192710706](https://i.loli.net/2020/06/27/pCvwYFlWrIZAe8k.png)

---

> **注：**google 'mysql date format string', open reference manual

![image-20200627192204956](https://i.loli.net/2020/06/27/NKwt162q3pTGhSU.png)

---

### 5. 计算日期和时间 - Calculating Dates and Times 

```MYSQL
#在当前日期上加1天或1年
DATE_ADD(NOW(), INTERVAL 1 DAY)
DATE_ADD(NOW(), INTERVAL 1 YEAR)
DATE_ADD(NOW(), INTERVAL -1 DAY/YEAR)

#计算两个日期的间隔:只返回天数的间隔，而不是小时或分钟
SELECT DATEDIFF(NOW(), '2020-05-28') AS DIFF  >30

#计算分秒间隔
	#1.返回从零点计算的秒数
	TIME_TO_SEC('09:00') >32400
	#2.利用TIME_TO_SEC函数计算
	TIME_TO_SEC('09:00') - TIME_TO_SEC('09:02') >-120
```

---

### 6. IFNULL和COALESE函数 - The IFNULL and COALESCE Functions

#### <span style='color:red;font-weight:bold'>IFNULL: 其他值替换NULL</span>

```mysql
#如果发货人id是NULL，这个函数会返回‘Not assigned’;不是NULL，返回具体值
SELECT
	order_id,
	ISNULL(shipper_id, 'Not assigned') AS shipper
FROM orders
```

<span style='color:red;font-weight:bold'>COALESCE: 返回这堆值中的第一个非空值</span>

```mysql
#如果发货人id是NULL，返回comments，如果comments为NULL，返回'Not assigned'
SELECT
	order_id,
	COALESCE(shipper_id, comments, 'Not assigned') AS shipper
FROM orders
```

---

### 7. IF函数 - 单一test expression 

```mysql
SELECT 
	product_id,
    name,
    COUNT(*) AS orders,
    IF(COUNT(*) = 1, 'Once', 'Many Times') AS frequency
FROM products
JOIN order_items USING(product_id)
GROUP BY product_id,name
```

![image-20200627204406006](https://i.loli.net/2020/06/27/r8CfA5FByIjYtbQ.png)

---

### 8. CASE运算符 - 多个 

![image-20200627205021621](https://i.loli.net/2020/06/27/jxpdteQSYKfWwi2.png)

```mysql
#方法2：更便捷
SELECT 
	CONCAT(first_name, '' , last_name) AS customer,
    points,
    CASE
		WHEN points > 3000 THEN 'Gold'
		#错了！！WHEN 2000< points < 3000 THEN 'Silver'
		WHEN points >= 2000 THEN 'Silver'
		ELSE 'Bronze' 
	END AS category
FROM customers
ORDER BY points DESC
```

![image-20200627205852401](https://i.loli.net/2020/06/27/PC3mguNSjqaH7kA.png)

---

## 八、视图 - Views

### 1. 创建视图 - CREATE VIEW..AS

```MYSQL
#简化查询，可以当作表用；但VIEW 不储存数据，数据还在原表中

CREATE VIEW clients_balance AS
SELECT
	 c.client_id,
	 c.name,
	 SUM(invoice_total - payment_total) AS balance
 FROM clients c
 JOIN invoices i USING(client_id)
 GROUP BY client_id,name
```

---

### 2. 更新或删除视图 - Altering or Dropping Views 

**2.1 写DROP VIEW ...（名字），执行删除视图；然后再修改原来的视图代码，执行**

**2.2 CREATE OR REPLACE VIEW ...**

* <span style='color:red;font-weight:bold'>更推荐，不需要先删除视图, 可更新无数次</span>

**2.3 如果这个查询窗口没了，你无法拿到创建这个视图用到的查询怎么办？**

* 把该查询保存到views的sql文件的folder里![image-20200628162736004](https://i.loli.net/2020/06/28/qLUKh2p6aEI5nvD.png)

* 是否有团队源码控制？

  * <span style='color:red;font-weight:bold'>Recommend</span>YES: 把文件夹放到源码控制中，把文件放入Git存储库里并和別人分享存储库，别人也可以在他的电脑上重建该数据库

  * NO: 

    * 1.点击图标，视图会以编辑模式打开

      点击![image-20200628162356652](https://i.loli.net/2020/06/28/wYBJPRXxAtQViTu.png)

    * 2.再代码后面接着添加新内容![image-20200628163451939](https://i.loli.net/2020/06/28/EAvPZOGBD3ar2jV.png)

    * 3.写完后点击APPLY，弹出后继续点击APPLY

---

### 3. 可更新视图 - Updatable Views 

⚡含有`DISTINCT`, `Aggregate Functions(MIN/MAX/SUM/AVG)`, `GROUP BY/HAVING`, `UNION`的查询**不可以**更新数据；含有`INSERT`, `UPDATE`, `DELETE` 的语句中使用此类视图

#### a. 删除record

像普通表一样，打开新的query editor

    ```mysql
DELECT FROM invoice_with_balance
WHERE invoice_id = 1
    ```

#### b. 更新record

```mysql
UPDATE invoice_with_balance
#把日期设为两天后
SET due_date = DATE_ADD(due_date, INTERVAL 2 DAY)
WHERE invoice—id = 2
```

#### c. 插入新record「麻烦」

 只有当视图有所有基础表中要用到的列，才可以插入              

---

### 4. WITH OPTION CHECK子句 - THE WITH OPTION CHECK Clause 

<span style='color:red;font-weight:bold'> 当通过视图更新或删除数据时，一些record有时会消失</span>

<span style='color:red;font-weight:bold'>不希望UPDATE/DELETE删除行时，在视图</span>的代码界面，最后一行写上`WITH CHECK OPTION`

![image-20200628171123811](https://i.loli.net/2020/06/28/H3aGYU2IfJulkpK.png)

```mysql
UPDATE invoice_with_balance
SET payment_total = invoice_total
WHERE invoice—id = 2

#不输入WITH CHECK OPTION如下图可能遗失record
#输入后，如果该record会消失，执行上面UPDATE语句会提示错误，无法执行
```

之前：![image-20200628170648709](C:\Users\CCK\AppData\Roaming\Typora\typora-user-images\image-20200628170648709.png)

之后:

![image-20200628170722058](https://i.loli.net/2020/06/28/pMt1dR8aNBIVqw3.png)

---

### 5. 视图的优点 - Other Benefits of Views

#### a. 减小数据库设计change的impact

#### b. 限制基础表访问：restrict access to the data

#### c. 简化query

----

## 十二章、数据类型 - Data Types 

### 2. 字符串类型 - String Types (2:25)

---

#### CHAR():

​	存放固定长度字符，like州的缩写

#### VARCHAR():

​	存放可变长度的字符，like用户名、密码、email

> 虽然他们是字符串，也可以用来保存数字类型数据，比如邮编、电话号码

![image-20200722143747030](https://i.loli.net/2020/07/22/KbLkj1AsiaJlu3G.png)

![image-20200722144130137](https://i.loli.net/2020/07/22/wjGs9pNi1nruFDB.png)

**占用字符：**

​	English: 1type

​	European Middle-eastern: 2types

​	Asian: 3types [亚洲字符如中文日文]

---

 ### 3. 整数类型 - Integer Types 

---

![image-20200722145222571](https://i.loli.net/2020/07/22/ZdhUYcaLPmNDp9s.png)

**ZEROFILL：填0保持位数一致**

INT(4) --→ 0001

<span style='color:red;font-weight:bold'>注意只关系到MYSQL如何显示数字，不影响如何保存数字</span>

---

### 4. 定点数类型和浮点数类型 - Fixed-point and Floating-point Types

![image-20200722151541581](https://i.loli.net/2020/07/22/5J6FbeWuosR9mfd.png)

---

### 5. 布尔类型 - Boolean Types

![image-20200722151708926](https://i.loli.net/2020/07/22/3KtYsFOxMXRnu5f.png)

--

### 6. 枚举和集合类型 - Enum and Set Types(尽量避免使用)

1. ENUM(尽量避免使用)

   ENUM('SMALL', 'MEDIUM', 'LARGE') 只可以插入括号内的三个值

2. SET

---

### 7. 日期和时间类型 - Date and Time Types 

![image-20200722152506949](https://i.loli.net/2020/07/22/vSVFHMrPxjdqfZT.png)

---

### 8. 二进制大对象类型 - Blob Types 

<span style='color:red;font-weight:bold'>大的二进制数据：images／videos / pdf</span>

<span style='color:red;font-weight:bold'>关系型数据库主要用来处理结构化数据，而不是二进制数据</span>

![image-20200722153146444](https://i.loli.net/2020/07/22/TFNBPdogrEHWCYZ.png)

![image-20200722153310827](https://i.loli.net/2020/07/22/sRKFkAXM2aSzUrH.png)

---

### 9. JSON类型 - JSON Type 

衣服都是有尺码和颜色的。电视就有其他的属性，比如重量和大小。在这个表中添加若干列是不对的。当每个产品只有一个地方记录它的property时，这时可以使用json，很方便的给每个产品保存特性键值对

1. **JSON**

   ![image-20200722153820457](https://i.loli.net/2020/07/22/wbxYmaFeJWM5vrj.png)

2. **mysql内置函数**

   ![image-20200722154023104](https://i.loli.net/2020/07/22/hpwiaI8fOsDHNx4.png)

   <img src="https://i.loli.net/2020/07/22/sXekU8oYd6ijVIr.png" alt="image-20200722154201709" style="zoom:150%;" />

![image-20200722154308691](https://i.loli.net/2020/07/22/75dZYlbf8wk2HoR.png)

简单：

![image-20200722154347888](https://i.loli.net/2020/07/22/YSuMIJaVd1vn2A3.png)

array：如果不填[0]的话，会返回整个数组

![image-20200722154430038](https://i.loli.net/2020/07/22/us1hjpiOrKkVxJD.png)

嵌套对象：

![image-20200722154551261](https://i.loli.net/2020/07/22/MOhj7PQaplNSrzo.png)

如果想要去掉‘’sony‘’的引号，把`->`改为`->>`

**更新JSON属性：**![image-20200722154818709](https://i.loli.net/2020/07/22/GxzYesQ6KZdDON2.png)