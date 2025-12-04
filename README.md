### 584. Find Customer Referee

``` 
SELECT * FROM Customer WHERE referee_id != 2;

-- xử lý đúng:

WHERE referee_id != 2 OR referee_id IS NULL;

``` 
- Dòng nào có referee_id = 2 → Bị loại 

- Dòng nào có referee_id = 1, 3, 99… → Được giữ 

- Dòng nào có referee_id IS NULL → Không được giữ, vì: NULL != 2 trả về UNKNOWN, không phải TRUE và SQL WHERE chỉ giữ dòng nào có điều kiện là TRUE

### 1683. Invalid Tweets

```
select tweet_id from Tweets where LENGTH(content) > 15
```

- Nếu cần kiểm tra độ dài chuỗi (số ký tự), dùng LENGTH (hoặc LEN trong SQL Server).

    - MySQL/PostgreSQL: LENGTH

    - SQL Server: LEN

    - Oracle: LENGTH

- COUNT là hàm tổng hợp (aggregate function): Nó hoạt động trên một nhóm hàng và trả về một giá trị duy nhất (số lượng hàng hoặc giá trị không null). Trong khi đó, WHERE chỉ áp dụng điều kiện cho từng hàng riêng lẻ, nên không thể dùng COUNT trực tiếp trong WHERE.

- Để sử dụng COUNT cho điều kiện, cần:

    - Nhóm hàng bằng GROUP BY.

    - Lọc nhóm bằng HAVING.

### 1378. Replace Employee ID With The Unique Identifier

```
select empUni.unique_id, emp.name
from Employees as emp
LEFT JOIN EmployeeUNI as empUni on empUni.id = emp.id

SELECT column_name(s)
FROM table1
INNER JOIN table2
ON table1.column_name = table2.column_name;
```

**Note:** The LEFT JOIN keyword returns all records from the left table (Customers), even if there are no matches in the right table (Orders).

### 1581. Customer Who Visited but Did Not Make Any Transactions

```
select Visits.customer_id, count(Visits.visit_id) as count_no_trans
from Visits
left join Transactions
on Visits.visit_id = Transactions.visit_id
where Transactions.transaction_id is null
group by Visits.customer_id;
```
<img width="369" alt="Ảnh màn hình 2025-05-27 lúc 11 11 01" src="https://github.com/user-attachments/assets/fb568a04-f606-4d9b-812c-a29b72d86c8d" />


- Nếu một visit_id trong Visits không có bản ghi tương ứng trong Transactions, các cột từ Transactions (như transaction_id) sẽ trả về giá trị NULL.)

- GROUP BY Visits.customer_id chia các hàng thành các nhóm, mỗi nhóm tương ứng với một customer_id duy nhất. Sau đó, COUNT(Visits.visit_id) được tính cho từng nhóm, cho ra số lượt ghé thăm không có giao dịch của mỗi khách hàng.

### 197. Rising Temperature

```
select w1.id 
from Weather w1
join Weather w2 
on w1.recordDate = date_add(w2.recordDate, interval 1 day)
where w1.temperature > w2.temperature;
```

- Để so sánh nhiệt độ của một ngày với ngày trước đó, ta cần so sánh hai hàng trong cùng bảng Weather.

- Self-join được sử dụng bằng cách kết nối bảng Weather với chính nó, tạo ra hai bản sao (bí danh w1 và w2): w1: Đại diện cho ngày hiện tại (ngày cần kiểm tra nhiệt độ), w2: Đại diện cho ngày trước đó (ngày để so sánh).

- DATE_ADD(w2.recordDate, INTERVAL 1 DAY): Hàm này lấy ngày trong w2.recordDate và thêm 1 ngày, trả về ngày kế tiếp.

### 1661. Average Time of Process per Machine

```
select a1.machine_id, round(avg(a2.timestamp - a1.timestamp),3 ) as processing_time
from Activity a1
join Activity a2
on a1.machine_id = a2.machine_id 
and a1.process_id = a2.process_id
and a1.activity_type = 'start' and a2.activity_type = 'end'
group by a1.machine_id
```

- hai điều kiện a1.process_id = a2.process_id và a1.activity_type = 'start' AND a2.activity_type = 'end' được coi là điều kiện ràng buộc để đảm bảo truy vấn hoạt động chính xác theo yêu cầu bài toán.

### 1280. Students and Examinations

```
select 
    Students.student_id, 
    Students.student_name, 
    Subjects.subject_name, 
    COALESCE(count(Examinations.student_id), 0) as attended_exams
from Students
cross join Subjects
left join Examinations
on Examinations.subject_name = Subjects.subject_name
and Students.student_id = Examinations.student_id
group by Students.student_id, Students.student_name, Subjects.subject_name 
order by Students.student_id, Subjects.subject_name
```

- Trong SQL, khi sử dụng một hàm tổng hợp như COUNT (ở đây là COUNT(e.student_id)), tất cả các cột trong SELECT không sử dụng hàm tổng hợp phải được liệt kê trong GROUP BY.

- Hàm COALESCE trong SQL nhận một danh sách các biểu thức và trả về giá trị không NULL đầu tiên trong danh sách.

- Ở đây, COALESCE(COUNT(e.student_id), 0) có nghĩa là:

    - Nếu COUNT(e.student_id) trả về một giá trị (số lần thi, ví dụ 1, 2, 3,...), thì lấy giá trị đó.

    - Nếu COUNT(e.student_id) trả về NULL (hoặc không có bản ghi nào), thì lấy giá trị 0.

- Điều này đảm bảo rằng khi một học sinh không tham gia kỳ thi nào cho một môn học, kết quả vẫn trả về 0 thay vì NULL.

- Nếu xài count không và không dùng COALESCE thì code chạy vẫn đúng


### 570. Managers with at Least 5 Direct Reports

```
select e1.name
from Employee e1
join Employee e2
on e1.id = e2.managerId 
group by e1.id, e2.managerId 
having count(e2.managerId) >= 5

select e1.name
from Employee e1, Employee e2
where e1.id = e2.managerId 
group by e1.id, e2.managerId 
having count(e2.managerId) >= 5
```

- HAVING COUNT(e2.id) >= 5 lọc các nhóm (mỗi nhóm tương ứng với một quản lý) mà số lượng bản ghi trong nhóm (số nhân viên báo cáo) lớn hơn hoặc bằng 5.
Thời điểm thực hiện: Sau khi nhóm các bản ghi (sau GROUP BY).

### 1934. Confirmation Rate

```
select S.user_id , COALESCE(round(avg(C.action = 'confirmed'), 2),0) as confirmation_rate 
from Signups S
left join Confirmations C
on S.user_id = C.user_id
group by S.user_id
```
- c.action = 'confirmed': Trả về 1 (true) nếu action = 'confirmed', 0 (false) nếu action = 'timeout' hoặc NULL (khi không có bản ghi).

- AVG(c.action = 'confirmed'): Tính trung bình các giá trị 1 và 0, tương đương với (Số tin nhắn confirmed) / (Tổng số tin nhắn). Nếu không có tin nhắn (c.action là NULL), AVG trả về 0.

### 620. Not Boring Movies

```
select id, movie, description, rating 
from Cinema
where id%2 <> 0 and description <> 'boring'
order by rating desc;

```

- <> là ký hiệu chung để biểu hiện "khác" trong sql

- Ngoài ra có thể dùng !=. not in, not like

### 1251. Average Selling Price

```
select P.product_id, ifnull(round(sum(US.units * P.price) / sum(US.units),2),0) average_price
from Prices P
left join UnitsSold US
on P.product_id = US.product_id
and US.purchase_date  between P.start_date and P.end_date 
group by P.product_id
```

- Theo yêu cầu của bài toán, nếu một sản phẩm không có đơn vị bán, giá trung bình phải được trả về là 0. Để xử lý trường hợp này, chúng ta sử dụng hàm IFNULL để thay thế giá trị NULL bằng 0.

- Hàm IFNULL là một hàm được sử dụng để xử lý các giá trị NULL. Cú pháp của nó là: IFNULL(biểu_thức, giá_trị_thay_thế)

### 1633. Percentage of Users Attended a Contest

```
select R.contest_id, 
ROUND(COUNT(DISTINCT R.user_id) * 100.0 / (SELECT COUNT(*) FROM Users), 2) AS percentage
from Users U
inner join Register R 
on U.user_id = R.user_id 
group by R.contest_id
order by percentage desc, R.contest_id asc
```

###  1193. Monthly Transactions I

```
-- select concat(year(trans_date), "-", month(trans_date)) as month ,
-- CONCAT(YEAR(trans_date), "-", LPAD(MONTH(trans_date), 2, '0')) AS month,

select DATE_FORMAT(trans_date, '%Y-%m') AS month,
country, count(state) as trans_count, sum((state = 'approved') * state) as approved_count,
sum(amount) as trans_total_amount, sum((state = 'approved') * amount) as approved_total_amount
from Transactions
group by month, country
```

- Hàm DATE_FORMAT(trans_date, định_dạng) cho phép định dạng trực tiếp ngày tháng theo mẫu mong muốn. Để lấy YYYY-MM (năm-tháng với tháng luôn có 2 chữ số), có thể dùng định dạng %Y-%m.

- Hàm LPAD(chuỗi, độ_dài, ký_tự_thêm) thêm ký tự (thường là 0) vào bên trái chuỗi cho đến khi đạt độ dài mong muốn.

- LPAD(MONTH(trans_date), 2, '0'):
    - Đảm bảo tháng có độ dài 2 chữ số.
    - Nếu tháng là 1, LPAD(1, 2, '0') trả về 01.
    - Nếu tháng là 10, LPAD(10, 2, '0') vẫn trả về 10 (vì đã đủ 2 chữ số).
 
### 1174. Immediate Food Delivery II

```
select round(avg(order_date = customer_pref_delivery_date)*100,2) as immediate_percentage 
from Delivery
where (customer_id, order_date) in 
(
select customer_id, min(order_date)
from Delivery
group by customer_id)
```

- Lọc ra các đơn hàng đầu tiêng rồi mới kiểm tra điều kiện giao 
- Biểu thức: order_date = customer_pref_delivery_date trả về TRUE (1) nếu đúng và FALSE (0) nếu sai
- Tính giá trị trung bình của các giá trị 1 và 0 từ bước trước. Trung bình này chính là tỷ lệ đơn hàng giao ngay.
    - [0, 1, 0, 1] -> 0 + 1 + 0 + 1 = 2 -> số lượng hàng là 4 vậy 2/4 = 0.5 (tức 50% đơn hàng là giao ngay).


### 550. Game Play Analysis IV

```
SELECT 
    ROUND(
        COUNT(DISTINCT CASE 
            WHEN a2.event_date = DATE_ADD(a1.first_login, INTERVAL 1 DAY) 
            THEN a1.player_id 
            END) / COUNT(DISTINCT a1.player_id), 
        2
    ) AS fraction
FROM (
    SELECT player_id, MIN(event_date) AS first_login
    FROM Activity
    GROUP BY player_id
) a1
LEFT JOIN Activity a2 
    ON a1.player_id = a2.player_id 
    AND a2.event_date = DATE_ADD(a1.first_login, INTERVAL 1 DAY);
```

- CASE Statement: Kiểm tra điều kiện: a2.event_date = DATE_ADD(a1.first_login, INTERVAL 1 DAY).
    - Nếu đúng (tức là người chơi có đăng nhập vào ngày liền sau first_login), trả về a1.player_id.
    - Nếu sai (hoặc a2.event_date là NULL do không có bản ghi khớp), trả về NULL.


### 619. Biggest Single Number

```
  select max(num) as num
  from (
            SELECT num
            FROM MyNumbers
            GROUP BY num
            HAVING COUNT(*) = 1
        ) as bangtam
```
- COUNT(num) không dùng được trong WHERE

- COUNT() là hàm tổng hợp → chỉ dùng sau khi gom nhóm (GROUP BY) và lọc bằng HAVING, hoặc phải đặt trong subquery/window.


### 1070. Product Sales Analysis III

```

select product_id, year as first_year, quantity, price
from Sales 
where (product_id, year) in 
(
    select product_id, min(year)
    from sales
    group by product_id
)

```

### 1045. Customers Who Bought All Products

```
select C.customer_id
from Customer C join Product P
where C.product_key = P.product_key
group by C.customer_id
HAVING COUNT(DISTINCT c.product_key) = (SELECT COUNT(*) FROM Product)

```

- SELECT COUNT(*) FROM Product (=2) -> Đây là số mốc để so sánh: ai mua đủ 2 loại sản phẩm thì đạt.
- COUNT(DISTINCT c.product_key) = số loại sản phẩm mỗi khách đã mua.
- Điều kiện HAVING ... = ... chính là chốt lọc “mua đủ”.

### 1731. The Number of Employees Which Report to Each Employee

```
WITH agg AS (
  SELECT r.reports_to AS employee_id,
         COUNT(*) AS reports_count,
         ROUND(AVG(r.age)) AS average_age
  FROM Employees r
  WHERE r.reports_to IS NOT NULL
  GROUP BY r.reports_to
)
SELECT a.employee_id, m.name, a.reports_count, a.average_age
FROM agg a
JOIN Employees m ON m.employee_id = a.employee_id
ORDER BY a.employee_id;
```

### 1789. Primary Department for Each Employee

```
SELECT employee_id, department_id
FROM
(
    SELECT employee_id,
    department_id,
    ROW_NUMBER() OVER
    (
        PARTITION BY employee_id 
        ORDER BY CASE WHEN primary_flag = 'Y' THEN 1 ELSE 0 END DESC
    ) AS row_num
    FROM Employee
) AS employee_primary_rank
WHERE row_num = 1
```

## Tư duy với window function = nghĩ theo 4 bước cố định
**- 1. Xác định “bài toán theo nhóm” → PARTITION BY**

Hỏi: mỗi kết quả được quyết định trong phạm vi của những hàng nào?

Trả lời = khóa nhóm (vd: theo employee_id, theo order_id, theo account_id…)

**- 2. Xác định “ưu tiên/xếp hạng trong nhóm” → ORDER BY (trong window)**

Hỏi: trong nhóm đó, tiêu chí chọn hàng “đúng” là gì?

Mã hoá bằng ORDER BY, thường kèm CASE WHEN để ép thứ tự (vd: Y trước N, mới nhất trước cũ…).

**- 3. Chọn “loại thước đo” → hàm cửa sổ phù hợp**

Cần chọn 1 hàng tốt nhất → ROW_NUMBER() (loại bỏ ties).

Có thể có đồng hạng → RANK()/DENSE_RANK().

Cần so sánh hàng kề → LAG()/LEAD().

Cần lũy kế/trượt → SUM()/AVG() OVER (...) + frame (ROWS/RANGE).

**- 4. Lọc/giữ hàng cần → lọc sau khi tính window**

Ngoài ra, hàm window không được dùng trong WHERE theo chuẩn SQL. Vì vậy nên phải bọc subquery/CTE (để row_num đã được tính xong), rồi lọc ở ngoài

## Window function khác với CTE chỗ nào
**CTE**

- một “bảng tạm đặt tên” được tạo ra từ một subquery để tái dùng, làm rõ logic, hoặc đệ quy. Không tự tạo giá trị mới ngoài những gì query con trả về.
- chia bài toán thành bước; có thể nối nhiều CTE; có thể đệ quy (WITH RECURSIVE).
- WITH cte AS (SELECT ...) SELECT ... FROM cte.
  
**Window function**
- hàm (ROW_NUMBER, RANK, SUM() OVER, AVG() OVER, LAG/LEAD, …) tính toán dựa trên các dòng liên quan (partition/ordering) mà không gộp/giảm số dòng.
- làm thống kê theo nhóm, xếp hạng, so sánh giữa dòng hiện tại và dòng trước/sau, rolling sum/avg, v.v.
- SUM(col) OVER (PARTITION BY ... ORDER BY ... ROWS BETWEEN ...).



### 610. Triangle Judgement

```
select x, y, z, 
        case 
            when x + y > z and y + z > x and x + z > y then 'Yes'
            else 'No'
        end as triangle
from Triangle;
```

### 180. Consecutive Numbers

```
select distinct num as ConsecutiveNums 
from ( 
    SELECT num, 
    LAG(num,1) OVER (ORDER BY id) AS gtri_trc_1, 
    LAG(num,2) OVER (ORDER BY id) AS gtri_trc_2 
    FROM Logs 
    ) bang_tam 
where num = gtri_trc_1 and num = gtri_trc_2
```
### 1164. Product Price at a Given Date

```
WITH latest_price AS (
    SELECT 
        product_id, 
        new_price,
        ROW_NUMBER() OVER (
            PARTITION BY product_id 
            ORDER BY change_date DESC
        ) AS bang_gia_moi_nhat
    FROM Products
    WHERE change_date <= '2019-08-16'
)

SELECT 
    p1.product_id,
       COALESCE(latest_price.new_price, 10) AS price
FROM (SELECT DISTINCT product_id FROM Products) p1
LEFT JOIN latest_price 
    ON p1.product_id = latest_price.product_id 
   AND latest_price.bang_gia_moi_nhat = 1
ORDER BY p1.product_id;
```

### 1204. Last Person to Fit in the Bus
```
SELECT person_name
FROM (
SELECT person_name,
    SUM(weight) OVER (ORDER BY turn) AS total_weight
FROM Queue
) AS ranked
WHERE total_weight <= 1000
ORDER BY total_weight desc
LIMIT 1
```

### 1907. Count Salary Categories
```
SELECT 
    'Low Salary' AS category,
    SUM(income < 20000) AS accounts_count
FROM Accounts

UNION ALL

SELECT 
    'Average Salary',
    SUM(income >= 20000 AND income <= 50000)
FROM Accounts

UNION ALL

SELECT 
    'High Salary',
    SUM(income > 50000)
FROM Accounts;
```


### 626. Exchange Seats
```
SELECT 
    id,
    CASE 
        WHEN id % 2 = 0 THEN 
            (SELECT student FROM Seat WHERE id = s1.id - 1)        -- ghế chẵn: lấy người ghế trước
        WHEN id % 2 = 1 THEN 
            COALESCE(
                (SELECT student FROM Seat WHERE id = s1.id + 1),   -- ghế lẻ: cố lấy ghế sau
                s1.student                                         -- nếu không có → giữ nguyên
            )
    END AS student
FROM Seat s1
ORDER BY id;
```

### 341. Movie Rating

Yêu cầu trả về 2 dòng trong 1 cột results:

Tên người dùng đã rating nhiều phim nhất
→ Nếu bằng nhau → chọn tên nhỏ nhất theo thứ tự từ điển (A trước B)
Tên bộ phim có điểm trung bình cao nhất trong tháng 02/2020
→ Nếu bằng nhau → chọn tên phim nhỏ nhất theo thứ tự từ điển

```
(SELECT u.name AS results
 FROM MovieRating mr
 JOIN Users u ON mr.user_id = u.user_id
 GROUP BY u.user_id, u.name
 ORDER BY COUNT(*) DESC, u.name ASC
 LIMIT 1)

UNION ALL

(SELECT m.title AS results
 FROM MovieRating mr
 JOIN Movies m ON mr.movie_id = m.movie_id
 WHERE mr.created_at >= '2020-02-01'
   AND mr.created_at < '2020-03-01'
 GROUP BY m.movie_id, m.title
 ORDER BY AVG(mr.rating) DESC, m.title ASC
 LIMIT 1);
```

### 1321. Restaurant Growth

```
-- Bước 1: Gộp tổng tiền mỗi ngày trước (bắt buộc!)
WITH daily AS (
    SELECT visited_on, SUM(amount) AS day_amount
    FROM Customer
    GROUP BY visited_on
)

-- Bước 2: Với mỗi ngày, tính tổng 7 ngày gần nhất bằng correlated subquery
SELECT 
    d1.visited_on,
    
    -- Tổng tiền trong 7 ngày (từ 6 ngày trước đến hôm nay)
    (
        SELECT SUM(d2.day_amount)
        FROM daily d2
        WHERE d2.visited_on BETWEEN DATE_SUB(d1.visited_on, INTERVAL 6 DAY) AND d1.visited_on
    ) AS amount,
    
    -- Trung bình 7 ngày, làm tròn 2 chữ số
    ROUND(
        (
            SELECT SUM(d2.day_amount)
            FROM daily d2
            WHERE d2.visited_on BETWEEN DATE_SUB(d1.visited_on, INTERVAL 6 DAY) AND d1.visited_on
        ) / 7.0, 2
    ) AS average_amount

FROM daily d1

-- Chỉ lấy những ngày đã đủ 7 ngày dữ liệu
WHERE d1.visited_on >= (
    SELECT MIN(visited_on) + INTERVAL 6 DAY FROM daily
)

ORDER BY d1.visited_on;
```
