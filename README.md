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
