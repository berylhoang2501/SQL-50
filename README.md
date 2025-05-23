`` ` 
SELECT * FROM Customer WHERE referee_id != 2;

# xử lý đúng:

WHERE referee_id != 2 OR referee_id IS NULL

`` ` 
- Dòng nào có referee_id = 2 → Bị loại 

- Dòng nào có referee_id = 1, 3, 99… → Được giữ 

- Dòng nào có referee_id IS NULL → Không được giữ, vì: NULL != 2 trả về UNKNOWN, không phải TRUE và SQL WHERE chỉ giữ dòng nào có điều kiện là TRUE

