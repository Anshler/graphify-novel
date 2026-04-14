# graphify-novel: Writing assistant với knowledge graph

Cứ viết. Đừng lo tracking.

Khi câu chuyện ngày càng dài, gánh nặng quản lý cũng tăng theo. Nhân vật này xuất hiện lần cuối ở đâu, rule đó được thiết lập chưa, những mạch truyện nào còn bỏ ngỏ? ```graphify-novel``` xử lý tất cả điều đó và bạn chỉ cần tập trung viết thôi.

Cung cấp một tiền đề và nó sẽ dựng khung truyện cho bạn. Đưa vào một chương truyện và nó sẽ check mâu thuẫn, flag các tình tiết chưa giải quyết, và tự động update mọi nhân vật, tuyến truyện. Đặt câu hỏi và nó truy vấn các kết nối xuyên suốt toàn bộ câu chuyện.

Skill này sử dụng [graphify](https://github.com/safishamsi/graphify) bên dưới để trích xuất knowledge graph từ các chương và bible, ánh xạ toàn bộ mối quan hệ giữa nhân vật, địa điểm, sự kiện và chủ đề. Đây là nền tảng cho việc truy vấn giữa nhiều chương, phát hiện cấu trúc, và gợi ý kết nối ẩn trong quá trình review.

---

## Yêu cầu

- Bất kỳ AI coding assistant nào (Copilot, Claude, v.v.)

- Skill [graphify](https://github.com/safishamsi/graphify) đã được cài đặt. Nếu chưa, assistant sẽ giúp bạn cài trước khi tiếp tục.

---

## Cài đặt

```bash
npx skills add Anshler/graphify-novel
```

---

## Cấu trúc project

```
<your-project>/
  chapters/          ← file bản thảo (nên xài .txt hoặc .md)
  draft/             ← bản nháp (không đưa vào graph)
  static/            ← file tĩnh, ảnh, v.v. (không đưa vào graph)
  bible/
    premise.md
    timeline.md
    characters/
    threads/
    world/
  graphify-out/      ← knowledge graph được tạo ra
  .graphifyignore
```

Bạn có thể bắt đầu với một dự án trống. Nếu là dự án có sẵn, hãy đảm bảo các chương đã hoàn thành nằm trong `chapters/`, bản nháp đang viết trong `draft/` và file tĩnh trong `static/`.

Nếu bạn đã có sẵn cấu trúc tracking câu chuyện, nó sẽ không tương thích. Hãy chuyển chúng ra ngoài dự án rồi copy paste nội dung đó vô trúc mới sau khi khởi tạo bible.

---

## Cách dùng

Chạy lệnh trong thư mục project, trong khung chat với AI assistant.

### Khởi tạo bible từ tiền đề (fresh start)
```
/graphify-novel init "Một người lưu trữ bị thất sủng phát hiện hồ sơ thành lập đế chế bị làm giả, và bằng chứng bị giấu trong một hầm chỉ kẻ tội đồ mới mở được."

/graphify-novel init --from premise.txt
```

### Khởi tạo bible từ các chương có sẵn

```
/graphify-novel init --from-chapters

/graphify-novel init --from-chapters --batch 3
```
Command này sẽ dựng khung `bible/`, điền vào các file nhân vật, tuyến truyện, thế giới và xây dựng knowledge graph ban đầu.

`--from-chapters` dùng để chuyển đổi từ câu chuyện có sẵn. Nó quét tất cả file trong `chapters/` theo từng batch (mặc định: 5) dùng sub-agent, mỗi sub-agent chạy trong context riêng để số chương không bị giới hạn bởi context window. Dùng `--batch N` để thay đổi kích thước batch.

### Review bản nháp để check tính nhất quán
```
/graphify-novel review ch03.md
/graphify-novel review ch03.md --intent "thiết lập rằng Elara biết nhiều hơn cô ấy thừa nhận"
/graphify-novel review --passage
```

Flag các mâu thuẫn và khoảng trống trong cốt truyện. Để review tốt hơn, hãy nêu rõ mục tiêu của bạn đối với chương truyện.

Command này sẽ không update bible hay graph, các phát hiện chỉ là đề xuất thôi.

Khi dùng `--passage`, AI sẽ nhắc bạn dán đoạn văn vào chat.

### Cập nhật knowledge graph sau khi viết
```
/graphify-novel update ch03.md
/graphify-novel update ch03.md --intent "thiết lập rằng Elara biết nhiều hơn cô ấy thừa nhận"
/graphify-novel update --lore "Hiệp ước Ràng Buộc được ký 40 năm trước khi câu chuyện bắt đầu"
/graphify-novel update --manual
```

Khuyên nên dùng sau khi đã review bản nháp.

Trong chế độ `--manual`, AI sẽ hội thoại với bạn để xác định thay đổi.

### Kiểm tra status câu chuyện trước khi bắt đầu một chương
```
/graphify-novel status
```
Hiển thị các tuyến truyện đang mở, trạng thái nhân vật, các setup chưa giải quyết (súng Chekhov), và các hub cấu trúc từ knowledge graph.

### Quản lý tuyến truyện
```
/graphify-novel thread new "Bí mật của Người kế thừa"
/graphify-novel thread resolve binding-pact
/graphify-novel thread list
```

### Truy vấn mối quan hệ liên chương
```
/graphify-novel query "Elara kết nối với Ngai vàng Obsidian như thế nào?"
/graphify-novel query "Chương 3 và chương 8 có điểm chung gì về mặt chủ đề?" --dfs
/graphify-novel path "Elara" "Pháo đài Đen"
```

---

## Lệnh

| Lệnh | Mô tả |
|------|-------|
| `/graphify-novel init "<premise>"` | dựng khung bible từ tiền đề |
| `/graphify-novel init --from <file>` | dựng khung bible từ file tiền đề |
| `/graphify-novel init --from-chapters [--batch N]` | xây dựng bible bằng cách quét các chương có sẵn (batch mặc định: 5) |
| `/graphify-novel review [file] [--intent "..."]` | review chương/đoạn văn so với bible |
| `/graphify-novel review --passage` | dán đoạn văn vào chat (AI assistant sẽ nhắc) |
| `/graphify-novel update [file] [--intent "..."]` | cập nhật bible từ một chương |
| `/graphify-novel update --manual ["..."]` | cập nhật tương tác |
| `/graphify-novel update --lore "<lore>"` | thêm/sửa lore trong bible |
| `/graphify-novel query "<question>"` | truy vấn mối quan hệ xuyên suốt story graph |
| `/graphify-novel query "<question>" --dfs` | depth-first — truy vết một đường cụ thể |
| `/graphify-novel path "Node A" "Node B"` | kết nối ngắn nhất giữa hai yếu tố câu chuyện |
| `/graphify-novel status` | tuyến truyện đang mở, trạng thái nhân vật, các mục chưa giải quyết |
| `/graphify-novel thread new "<name>"` | tạo một tuyến truyện mới |
| `/graphify-novel thread resolve <slug>` | đánh dấu một tuyến truyện đã giải quyết |
| `/graphify-novel thread list` | liệt kê tất cả tuyến truyện với trạng thái |

---

## Cách hoạt động

`bible/` là nguồn ground truth — các file trạng thái có cấu trúc để theo dõi thông tin chính xác về từng nhân vật, tuyến truyện và yếu tố thế giới.

`graphify-out/` là lớp quan hệ — knowledge graph được trích xuất từ toàn bộ bản thảo và bible. Nó làm nổi bật các kết nối ẩn và các mẫu cấu trúc mà việc đọc file trực tiếp sẽ không phát hiện được.

Hai thứ này không thay thế nhau. Bible trả lời "Elara hiện ở đâu?"; graph trả lời "Elara và Ngai vàng Obsidian kết nối với nhau như thế nào?"

Lưu ý: ```.graphifyignore``` cho phép bạn loại bỏ file/thư mục nhất định khỏi graph. Hiện tại nó đang bỏ qua:
```
bible/characters/
bible/threads/
bible/timeline.md
```

Vì chúng là thông tin lặp lại từ các chương, hoặc là để track thời gian tuyến tính không phù hợp với graph. Bạn có thể chỉnh sửa theo ý muốn.

### Mẹo

Nếu bạn không chắc nên dùng lệnh nào, cứ chat bằng ngôn ngữ tự nhiên — hỏi AI tìm hiểu về skill graphify-novel và trả lời bạn.
