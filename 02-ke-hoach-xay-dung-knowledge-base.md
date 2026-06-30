# Kế hoạch xây dựng Knowledge Base (KB) với NotebookLM

**Mục tiêu:** Tạo một "bộ não chung" cho team — nơi mọi người hỏi bằng ngôn ngữ tự nhiên và nhận câu trả lời **có trích dẫn nguồn**, thay vì đi tìm file thủ công.

---

## ⚠️ Đọc trước khi bắt tay làm (quan trọng nhất)

NotebookLM đã có **tự động đồng bộ với Google Drive (từ 05/2026)**, nhưng:

> **Chỉ tự cập nhật với file Google gốc: Google Docs, Sheets, Slides.**
> File **PDF, `.md`, văn bản dán tay → KHÔNG tự cập nhật** (đứng yên tại thời điểm import).

Hệ quả: nếu để runbook ở dạng `.md` hoặc `.pdf`, ai đó sửa runbook nhưng NotebookLM vẫn trả lời theo bản cũ → **rất nguy hiểm khi đang xử lý sự cố**.

**→ Quy tắc bắt buộc:** Tài liệu hay thay đổi (Manual, Monitoring, Incident) phải viết dưới dạng **Google Docs / Sheets**, KHÔNG dùng `.md` hay PDF. Chỉ dùng `.md`/PDF cho tài liệu gần như không bao giờ đổi.

---

## 4 Knowledge Base (chia theo vòng đời tài liệu)

| KB | Loại tài liệu | Đặc điểm |
|---|---|---|
| **01_Onboarding** | Giới thiệu, hướng dẫn nhập môn | Đọc 1 lần, ít đổi |
| **02_Manual** | Hướng dẫn vận hành, runbook | Tham khảo thường xuyên |
| **03_Incident** | Nhật ký sự cố | Chỉ thêm mới, lớn nhanh |
| **04_Monitoring** | Giám sát, dashboard | Tham khảo, có thay đổi |

---

## Các bước thực hiện

### Bước 1 — Tạo cấu trúc thư mục trên Google Drive

```
AI_KB/  (thư mục cha)
├── 01_Onboarding/
├── 02_Manual/
├── 03_Incident/
└── 04_Monitoring/
```

### Bước 2 — Chuẩn bị tài liệu (dạng Google Docs/Sheets)

Liệt kê tài liệu cho từng KB. **Viết dưới dạng Google Docs/Sheets để tự đồng bộ.**

- **01_Onboarding:** `Org_Overview`, `Security_Training`
- **02_Manual:** `Chrome_Vup_Test`, `RUNBOOK_Deploy_ServiceA`
- **03_Incident:** `Incident_Template`, và **1 file Sheet tổng** ghi mọi sự cố (xem lưu ý bên dưới)
- **04_Monitoring:** `Monitoring_Overview`, `Dashboard_List`

> **Lưu ý cho Incident:** ĐỪNG tạo mỗi sự cố một file riêng (sẽ chạm giới hạn 50 nguồn/notebook trong 1-2 năm). Hãy dùng **1 Google Sheet duy nhất, ghi dồn các sự cố theo dòng**. Vừa không lo giới hạn, vừa dễ lọc ("tất cả sự cố database quý này").
> Quy ước đặt tên dòng/sự cố: `INC_YYYY_###_TenSuCo`.

### Bước 3 — Tạo NotebookLM

Tạo **4 NotebookLM**, mỗi cái nạp nguồn từ 1 thư mục KB tương ứng.

> **Cần biết về giới hạn của NotebookLM:**
> - Một notebook **không hỏi chéo sang notebook khác được.**
> - Câu hỏi giá trị nhất thường nằm vắt qua nhiều KB: *"Đang có cảnh báo X (Monitoring) — runbook xử lý là gì (Manual) — trước đây gặp chưa (Incident)?"*
> - **Khuyến nghị:** Bắt đầu với 4 notebook (đúng với cách nghĩ của team, trả lời chính xác hơn). Khi nào thấy mọi người **bối rối không biết hỏi notebook nào** → đó là dấu hiệu nên gộp lại thành 1.

| Giới hạn (06/2026) | Free | Plus |
|---|---|---|
| Số nguồn / notebook | 50 | 100 |
| Số notebook | 100 | nhiều hơn |

### Bước 4 — Soạn "Câu hỏi click sẵn"

NotebookLM **không có nút câu hỏi cố định** cho người dùng. Cách làm thực tế:

> Tạo một **Note tên "Câu hỏi thường gặp"** ghim đầu mỗi notebook, liệt kê sẵn các câu hỏi chuẩn. Người dùng **copy-paste** để hỏi (vừa là câu hỏi mẫu, vừa là tài liệu hướng dẫn).

Ví dụ cho Onboarding:
- "Quy trình onboarding nhân viên mới gồm những bước nào?"
- "Tôi xin nghỉ phép bằng cách nào?"

### Bước 5 — Kiểm tra trước khi phát hành (KHÔNG được bỏ)

Chạy thử toàn bộ "câu hỏi thường gặp", xác nhận:
- [ ] Câu trả lời **đúng**.
- [ ] Có **trích dẫn đúng tài liệu**.

> Trong vận hành, một câu trả lời sai mà tự tin = một sự cố. Bước kiểm tra này là bắt buộc.

---

## Phân công người phụ trách (KB không ai giữ sẽ mục nát)

| KB | Người phụ trách | Việc định kỳ |
|---|---|---|
| 01_Onboarding | _(điền)_ | Cập nhật khi quy trình đổi |
| 02_Manual | _(điền)_ | Cập nhật khi runbook đổi |
| 03_Incident | _(điền)_ | **Mỗi sự cố → ghi vào Sheet** |
| 04_Monitoring | _(điền)_ | Cập nhật khi dashboard đổi |

---

## Bảo mật & phân quyền

> NotebookLM **tuân theo quyền của thư mục Google Drive**. Nếu thu hồi quyền truy cập file, nguồn đó bị gỡ khỏi notebook.

→ **Quyền của thư mục Drive chính là lớp kiểm soát truy cập.** Khóa chặt thư mục `03_Incident` và file `Security_Training` cho đúng người.

---

## NotebookLM vs. Gemini trong Drive (dùng bổ sung cho nhau)

| | Dùng khi nào | Đặc điểm |
|---|---|---|
| **Gemini trong Drive** | "File đó nằm đâu?" | Tìm khắp Drive, rộng, ít trích dẫn |
| **NotebookLM** | "Runbook nói chính xác gì?" | Phạm vi gọn, **trích dẫn rõ ràng** |

Với KB cần câu trả lời đáng tin → ưu tiên **NotebookLM** (vì có trích dẫn nguồn).

---

## Tóm tắt 1 dòng

Cấu trúc 4 KB là đúng. Chỉ cần: **dùng Google Docs thay cho `.md`/PDF**, gộp sự cố vào **1 Sheet**, thêm **bước kiểm tra** và **người phụ trách** — KB sẽ "sống" thay vì "đóng băng", và trở thành sân tập thật cho Buổi 3 của khóa đào tạo AI.
