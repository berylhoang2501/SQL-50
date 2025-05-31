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

Nếu COUNT(e.student_id) trả về một giá trị (số lần thi, ví dụ 1, 2, 3,...), thì lấy giá trị đó.

Nếu COUNT(e.student_id) trả về NULL (hoặc không có bản ghi nào), thì lấy giá trị 0.

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

