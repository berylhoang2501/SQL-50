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

MySQL/PostgreSQL: LENGTH

SQL Server: LEN

Oracle: LENGTH

- COUNT là hàm tổng hợp (aggregate function): Nó hoạt động trên một nhóm hàng và trả về một giá trị duy nhất (số lượng hàng hoặc giá trị không null). Trong khi đó, WHERE chỉ áp dụng điều kiện cho từng hàng riêng lẻ, nên không thể dùng COUNT trực tiếp trong WHERE.

- Để sử dụng COUNT cho điều kiện, cần:

Nhóm hàng bằng GROUP BY.

Lọc nhóm bằng HAVING.

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
