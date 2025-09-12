# mongodb-shell
Chuẩn bị nhanh
Máy có MongoDB đang chạy (local: mongod), mở terminal và gõ mongosh để vào shell.
Nếu dùng Atlas thì mongosh "mongodb+srv://<user>:<password>@cluster0.xyz.mongodb.net" (thay user/password).
Mở terminal to, font rõ — khi demo dùng Ctrl+L để clear màn hình (terminal thường hỗ trợ).
# 1. Kết nối & chuyển DB
## Hướng dẫn MongoDB Shell

Mở **mongosh** và chạy các lệnh sau:

```javascript
show dbs            // hiển thị các database
use demoDB          // chuyển (hoặc tạo) database demoDB
db                  // in tên DB hiện tại
```
## Tạo collection + Insert (Create)
// tạo collection (không bắt buộc, insert tự tạo collection)
```javascript
db.createCollection("products")
```
```javascript
// insert nhiều document mẫu
db.products.insertMany([
  { name: "Bánh mì",    price: 15000, category: "food",  stock: 100, tags: ["breakfast","viet"] },
  { name: "Phở bò",     price: 50000, category: "food",  stock: 50,  tags: ["lunch","viet"] },
  { name: "Cà phê sữa", price: 20000, category: "drink", stock: 200, tags: ["drink","coffee"] }
])

// insert 1 document
db.products.insertOne({ name: "Trà đá", price: 5000, category: "drink", stock: 500 })
```
## Đọc dữ liệu (Read)
```javascript
// hiển thị tất cả 
db.products.find()

// chỉ 1 document
db.products.findOne({ name: "Phở bò" })

// filter: giá < 30000
db.products.find({ price: { $lt: 30000 } })

// projection: chỉ hiện name + price, ẩn _id
db.products.find({}, { name: 1, price: 1, _id: 0 })

// sort + limit
db.products.find().sort({ price: -1 }).limit(2)

// đếm
db.products.countDocuments()
db.products.countDocuments({ category: "food" })
```
## 4. Cập nhật (Update)
```javascript
// sửa 1 field: phải dùng operator như $set hoặc $inc
db.products.updateOne({ name: "Bánh mì" }, { $set: { price: 16000 } })

// giảm stock 1 đơn vị (mô phỏng bán 1 cái)
db.products.updateOne({ name: "Cà phê sữa" }, { $inc: { stock: -1 } })

// update nhiều document cùng lúc
db.products.updateMany({ price: { $lt: 20000 } }, { $set: { discount: true } })

// upsert: nếu không tồn tại thì insert
db.products.updateOne({ name: "Bún chả" }, { $set: { price: 35000, category: "food" } }, { upsert: true })

// replace (thay toàn bộ document) — dùng cẩn thận: sẽ thay hết các field (nhớ giữ _id nếu cần)
db.products.replaceOne({ name: "Phở bò" }, { name: "Phở bò", price: 45000, category: "food", spicy: true })
```
## 5. Xóa (Delete)
```javascript
// xóa 1 document
db.products.deleteOne({ name: "Bánh mì" })

// xóa nhiều document (ví dụ giá < 20000)
db.products.deleteMany({ price: { $lt: 20000 } })

// xóa collection
db.products.drop()

// xóa database hiện tại
db.dropDatabase()
```
## Link tham khảo thêm
https://www.thanhnamnguyen.dev/tai-lieu/cheatsheet/database/mongodb/
