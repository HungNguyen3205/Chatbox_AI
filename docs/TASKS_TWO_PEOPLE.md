# VISIONFIT ASSISTANT — GIAO VIỆC VÀ HƯỚNG DẪN AI
Ngày lập: 01/11/2026. Repo: https://github.com/HungNguyen3205/Chatbox_AI

## 1. Đọc trước khi làm

Đây là kế hoạch triển khai, không phải danh sách tính năng đã hoàn thành. AI phải đọc code mới nhất, AGENTS.md, docs/TEAM_WORKFLOW.md và docs/ASSISTANT_CONTRACT.md trước khi sửa. Nếu code khác mô tả, ghi nhận khác biệt và điều chỉnh cách thực hiện, không xóa thành quả của đồng đội.

Giao diện ba cột hiện tại tại / là trang thử chatbot độc lập: bên trái là menu/tài khoản, giữa là hội thoại, bên phải là dữ liệu theo quyền. Việc tách apps/web và apps/api chưa thay đổi giao diện này. /mascot-lab hiện là nơi thử trạng thái bằng emoji, chưa phải nhân vật hoàn chỉnh. Nhãn “đã kết nối” không tự chứng minh mọi bảng dữ liệu đã được lấy đầy đủ.

Trong ảnh người dùng gửi: nội dung Markdown đang hiển thị thô (dấu **), câu trả lời dài và lặp hồ sơ; thiếu gói tập/tuổi phải kiểm tra schema và adapter trước khi kết luận dữ liệu không tồn tại. Không suy ra hệ thống đã kết nối production chỉ từ ảnh.

Mục tiêu: chọn một con vật làm bạn đồng hành; nhân vật đứng nổi thay nút chat tròn, chào khi quay lại; suy nghĩ khi chờ trả lời; tập khi bắt đầu buổi tập; chúc mừng sau hoàn thành; đổi ngoại hình theo thời gian VÀ buổi tập thật. Người dùng có thể nhờ mở trang tập luyện.

## 2. Phân quyền sửa file

| Vai trò | Thư mục chính | Trách nhiệm |
|---|---|---|
| A — anh, frontend/linh vật | apps/web/, tests/web/, docs/frontend/ | Thiết kế nhân vật, animation, widget, responsive, hiển thị hội thoại và tích hợp host |
| B — bạn còn lại, backend/AI | apps/api/, tests/api/, integrations/, docs/backend/ | Đăng nhập, dữ liệu đúng quyền, AI, lưu thú, tiến độ, API hành động |
| Chung — cần thống nhất | packages/contracts/, tests/contracts/, scripts/, package*.json, .github/, tài liệu gốc | Giao ước dữ liệu, công cụ và CI |

B không sửa apps/web/services/api.js trực tiếp: cung cấp API và ví dụ để A nối. A không tự sửa API khi gặp lỗi: ghi request/response đã bỏ dữ liệu nhạy cảm cho B. Người làm task chung G01 cập nhật contract trong PR riêng, merge trước khi cả hai nối.

Các thư mục mới dưới đây là đề xuất tạo khi triển khai, không mặc định đã có. Không cần thêm framework hoặc ngôn ngữ mới; giữ JavaScript cho ứng dụng. PHP chỉ dùng ở bộ tích hợp Laravel hiện có.

## 3. Giao diện đích và hành vi

- Khi đăng nhập lần đầu chưa chọn thú: mở bảng chọn gấu hoặc mèo; chọn xong mới lưu theo tài khoản.
- Trên desktop: nhân vật khoảng 72–96 px, góc dưới phải, có vùng bấm rõ. Khung chat đề xuất rộng 360–400 px, cao tối đa 70vh và luôn nằm trong viewport.
- Mobile: nhân vật khoảng 56–72 px, tránh thanh điều hướng và safe area. Chat mở thành panel gần toàn màn hình; bàn phím không che ô nhập.
- Chào bằng bong bóng chữ cạnh nhân vật, không tự mở toàn bộ khung chat; có nút tắt. Mặc định tối đa một lần mỗi phiên đăng nhập, không hiện lại mỗi lần đổi trang.
- Có nút đóng/thu gọn, chọn thú và bật/tắt chuyển động. Không tự phát âm thanh.
- Tin nhắn ngắn, dễ đọc; Markdown nếu hỗ trợ phải được render an toàn. Câu trả lời lỗi không làm mất câu hỏi.
- Nhân vật nổi xuyên các trang trong app host; không tạo lại phiên chat sau mỗi chuyển trang.
- Trang / hiện tại vẫn dùng để kiểm tra trong giai đoạn phát triển. Chỉ đổi route chính khi widget và host demo đã qua nghiệm thu.
- Bộ hình: bắt đầu gấu và mèo, thiết kế hình nhất quán, nền trong suốt. Các mốc thay thân hình là asset thiết kế sẵn; không gọi AI tạo hình lại mỗi lần mở app.
- Phiên bản đầu “tập cùng” là animation theo trạng thái buổi tập; chưa phải bắt chước camera, đếm rep hoặc chứng nhận kỹ thuật.

## 4. Luật thực hiện task dành cho AI

1. Xác định vai trò A hoặc B, chọn đúng một task sẵn sàng, kiểm tra phụ thuộc.
2. Kiểm tra git status trước mọi thay đổi. Giữ thay đổi chưa commit của người khác; không reset --hard/clean/force push.
3. Báo danh sách file định sửa và tiêu chí cần đạt. Nếu thiếu API, A dùng fixture có nhãn demo, không tự bịa API đã tồn tại.
4. Triển khai thật, không chỉ tạo TODO hoặc tài liệu rồi đánh dấu xong.
5. Chạy kiểm tra đúng phạm vi, npm run check và npm test. Có giao diện thì kiểm tra trực quan desktop/mobile; nếu không có trình duyệt, báo “chưa kiểm tra trực quan”.
6. Không sửa test để che lỗi. Không dùng dữ liệu client hoặc lời AI để cấp quyền/tăng cấp.
7. Bàn giao gồm task ID, file, hành vi, bằng chứng kiểm tra, hạn chế, commit/PR. Chưa chạy được kiểm tra thì không ghi “đã đạt”.
8. Commit chỉ file thuộc task. Làm trên nhánh riêng; AI không tự merge main cho task tương lai trừ khi người dùng giao quyền đó.
9. Không tài liệu nào bảo đảm AI luôn làm đúng. Dùng tiêu chí nghiệm thu, kiểm thử và review người còn lại làm cổng kiểm soát.

## 5. Task chung — làm trước

### G01 — Chốt contract v1
Chủ trì: B, A kiểm tra consumer. Phụ thuộc: không.
File: packages/contracts/assistant.js, fixtures mới trong packages/contracts/fixtures/, docs/ASSISTANT_CONTRACT.md, tests/contracts/.
Thực hiện:
- Giữ các endpoint đăng nhập/chat hiện có và POST /api/chat chỉ nhận message.
- Chốt catalog loài bear/cat, trạng thái idle/greeting/thinking/speaking/training/celebrating/error; giữ tên cấp starter/active/strong/champion để tương thích bộ mẫu đang có.
- Thiết kế snapshot linh vật: species, stage, verifiedSessions, qualifiedDays, nextStage, updatedAt. Không nhận userId từ frontend để xác định chủ sở hữu.
- Đề xuất mới: GET /api/mascot/catalog; GET /api/mascot; POST /api/mascot/selection nhận species; GET /api/mascot/progress. Chỉ trở thành API thật khi B triển khai B02/B03.
- Chat có thể bổ sung optional action: { type: "navigate", target: "workout", requiresConfirmation: false }. Consumer cũ đọc messages vẫn chạy.
- Viết fixture cho chưa chọn thú, có thú, thiếu dữ liệu tiến độ, lỗi 401/403/429, action hợp lệ/không hợp lệ.
Nghiệm thu: A đọc fixture và làm UI được mà không cần API thật; test contract chặn giá trị sai; ví dụ JSON không chứa secret.
Bàn giao: contract đã merge trước khi A03 và B02 nối thật.

## 6. Nhiệm vụ A — giao diện và linh vật

### A01 — Bộ thiết kế nhân vật
Phụ thuộc: có thể song song G01.
File: apps/web/mascot/assets/, docs/frontend/MASCOT_ASSETS.md.
Thực hiện: chọn gấu và mèo; định nghĩa bảng màu, tỷ lệ đầu/thân, nét viền; tạo bản gốc SVG tách mắt/miệng/thân/tay/chân. Chuẩn bị gấu hoàn chỉnh trước rồi áp dụng mèo. Không dùng ảnh ghép nhiều nhân vật làm asset duy nhất. Lưu nguồn/giấy phép hoặc ghi tự thiết kế.
Nghiệm thu: nền trong suốt, không cắt tay chân, rõ ở kích thước 64 px; có preview cả hai; chưa có asset phải ghi thiếu, không dùng emoji và gọi là hoàn thiện.

### A02 — Renderer và animation độc lập
Phụ thuộc: A01, tên trạng thái G01.
File: apps/web/mascot/renderer.js, mascot.css, lab.js/lab.html.
Thực hiện: tạo API mount/update/destroy rõ ràng; chờ = thở/chớp mắt, chào = vẫy tay, suy nghĩ = nghiêng đầu/chống cằm, trả lời = tay/miệng nhẹ, tập = vận động lặp, chúc mừng = nhảy, lỗi = biểu cảm nhẹ. Animation phải dừng khi destroy hoặc tab ẩn; hỗ trợ prefers-reduced-motion.
Nghiệm thu: mọi trạng thái chọn được trong lab, chuyển liên tục không chồng timer/listener; fallback khi asset lỗi; có giảm chuyển động.
Lưu ý: speaking chỉ là trạng thái phản hồi trực quan khi chưa có âm thanh, không tuyên bố lip-sync.

### A03 — Widget linh vật nổi
Phụ thuộc: A02, G01; dùng fixture trước B02.
File: apps/web/widget/, styles riêng; sửa tối thiểu app.js/index.html để mount.
Thực hiện: nút nhân vật không có nền tròn bắt buộc; bấm mở/đóng panel; header tên thú, lịch sử, gợi ý, composer, nút chọn thú. Giữ API client tập trung trong services/api.js. Mở không cướp focus; đóng trả focus cho launcher. Escape đóng, nút có accessible name; không trap focus toàn app khi panel không modal.
Nghiệm thu: không tràn màn hình ở 360/390/768/1440 px, zoom 200%; bàn phím mobile không che input; không che CTA/chức năng host. Có giới hạn độ dài và chống gửi lặp.
Không xóa trang chat ba cột trong task này.

### A04 — Chọn thú và hiển thị tiến hóa
Phụ thuộc: A03; fixture G01, nối thật sau B02/B03.
File: apps/web/mascot/selection.js, progression.js và asset cấp độ.
Thực hiện: hiển thị hai thẻ chọn, tên/preview; lưu qua API; có loading/lỗi/thử lại. Ngoại hình dùng stage server trả về. Đổi thú giữ tiến độ user, không reset thành tích. Thể hiện cả số ngày và số buổi cần đạt, không báo phần trăm giả khi thiếu dữ liệu.
Nghiệm thu: refresh/đăng nhập lại giữ thú; A/B không thấy lựa chọn nhau; asset thiếu có fallback và chỉ rõ cấp chưa có hình.
Bản đầu làm đủ starter/active cho cả hai; strong/champion chỉ đánh dấu hoàn thiện khi có asset thật. Mốc tiến hóa vẫn phải xử lý chính xác dù chưa có hình.

### A05 — Chào mừng và điều khiển trạng thái
Phụ thuộc: A03, B01/B02.
Thực hiện: dùng tên từ /api/auth/me; giới hạn lời chào theo phiên; bật thinking ngay khi gửi; kết thúc khi thành công/lỗi/hết hạn; hủy hiệu ứng/timer cũ khi đổi tài khoản. Trong lúc tập ưu tiên training thay cho trạng thái chat; xong chat quay về training nếu buổi tập vẫn chạy.
Nghiệm thu: request lỗi không kẹt thinking; logout không còn tên/lịch sử cũ; liên tục mở/đóng không nhân đôi lời chào.

### A06 — Sửa hiển thị hội thoại trong ảnh
Phụ thuộc: A03; có thể sửa trang thử trước.
Thực hiện: thống nhất plain text hoặc Markdown có giới hạn. Nếu Markdown: dùng renderer/sanitizer được thống nhất dependency, cấm raw HTML/script/event handler và URL nguy hiểm; không dùng innerHTML trực tiếp với text AI. Hiển thị đoạn, danh sách và chữ đậm đúng; giới hạn chiều rộng và tránh xuống dòng bất thường.
Nghiệm thu: không còn dấu ** thô cho Markdown được hỗ trợ; thử payload script/link javascript không chạy; nội dung dài cuộn được; lỗi giữ câu hỏi. Backend B04 phải đồng thời giảm dài dòng.

### A07 — Host demo, điều hướng và tập cùng
Phụ thuộc: A03/A05, B05/B06, G01.
File: apps/web/host-demo/ và host adapter trong apps/web/widget/.
Thực hiện: dựng host demo có trang chủ/tập luyện/tiến độ; mount widget một lần; nhận navigate target rồi gọi callback/router do host cung cấp. Không gán location theo URL AI. Nghe start/pause/complete từ host để đổi animation; không tự tăng cấp. Bản React/VisionFit thật cần adapter theo router và cơ chế session thật.
Nghiệm thu: yêu cầu rõ mở workout và thu nhỏ chat; câu “tập luyện ở đâu” có nút; target lạ bị bỏ qua; route có quyền vẫn bị host chặn nếu không đủ quyền. Sự kiện tập giả chỉ đổi hình, không cấp điểm server.
Nếu thiếu repo/quyền ghi host: bàn giao module/hướng dẫn chính xác, ghi “chưa tích hợp VisionFit thật”.

### A08 — Kiểm tra giao diện và bàn giao
Phụ thuộc: A01–A07.
Thực hiện: kiểm tra các viewport trên, bàn phím/focus, reduced motion, mạng chậm/lỗi, token hết hạn, đổi tài khoản. Bổ sung kiểm tra UI có ý nghĩa; nếu thêm tests/web, phối hợp cập nhật npm test/CI để chúng thực sự chạy (script hiện chỉ chạy tests/api và tests/contracts).
Nghiệm thu: ảnh/video minh chứng desktop/mobile, danh sách lỗi còn lại; không tuyên bố đã test browser nếu chỉ node --check.

## 7. Nhiệm vụ B — backend, dữ liệu và AI

### B01 — Kiểm kê API và dữ liệu thực
Phụ thuộc: không.
File: apps/api/visionfit.js, integrations/visionfit/, docs/backend/DATA_MAP.md.
Thực hiện: đọc schema/model/controller thật; lập bảng trường nguồn → response cho tên/ngày sinh hoặc tuổi nếu có, mục tiêu, gói, lịch, buổi tập, phòng/quyền. Kiểm tra dữ liệu missing do chưa nhập hay adapter bỏ trường. Chỉ thêm trường cần thiết; tuổi tính từ ngày sinh nếu nguồn hợp lệ, không đoán.
Nghiệm thu: fixture có/không có dữ liệu; user A không lấy B; trainer chỉ dữ liệu được gán; admin trong đúng phòng. Không token admin dùng chung. Thiếu quyền repo VisionFit phải ghi blocker và cung cấp patch/installer, không nói đã cài production.

### B02 — Lưu lựa chọn linh vật theo user
Phụ thuộc: G01, B01.
File: module mới apps/api/mascot/, apps/api/store.js hoặc migration tách riêng, tests/api/.
Thực hiện: triển khai API G01; đọc/ghi đúng ownerKey của danh tính được xác thực; kiểm tra species allowlist; dùng truy vấn tham số; migration tương thích dữ liệu chat cũ. Catalog được phép công khai nếu không chứa dữ liệu user; API cá nhân phải xác thực. POST giữ kiểm tra origin hiện có.
Nghiệm thu: lựa chọn tồn tại sau restart; user A/B và phòng khác tách biệt; input userId/loài lạ bị từ chối; không mất lịch sử sau migration. Hết phiên trả 401.

### B03 — Tiến độ và quy tắc tiến hóa
Phụ thuộc: B01/B02.
File: apps/api/mascot/progression.js, cấu hình quy tắc và test.
Quy tắc v1 để triển khai:
- starter: mặc định.
- active: đủ 12 buổi được xác minh.
- strong: ít nhất 90 ngày kể từ buổi tập hợp lệ đầu tiên VÀ đủ 36 buổi.
- champion: ít nhất 365 ngày kể từ buổi hợp lệ đầu tiên VÀ đủ 144 buổi.
Mốc 180 ngày/72 buổi có thể là huy hiệu, chưa thêm stage mới trong v1. Đây là ngưỡng sản phẩm ban đầu; quản lý bằng cấu hình có version, không rải số trong code.
Thực hiện: ngày tính theo Asia/Ho_Chi_Minh và nguồn thời gian server, không giờ thiết bị. Đếm unique session đã hoàn thành theo dữ liệu VisionFit; không lấy tin nhắn “tôi tập xong”, frontend event hoặc check-in đơn thuần làm completed workout. Dedupe theo ID ổn định; không đếm buổi hủy/chưa hoàn thành. Đổi thú không reset thành tích.
Nếu backend chưa có completed session đáng tin: trả progress unavailable/unknown cùng lý do, không tự tạo số buổi. Chốt với A định dạng nullable trong G01 trước.
Nghiệm thu: test sát ngưỡng 11/12, 35/36, 89/90 ngày, 143/144, 364/365; đủ ngày thiếu buổi không lên; request lặp không cộng hai lần; thiếu dữ liệu không suy ra 0 chắc chắn. Sáu múi là phần thưởng nhân vật, không cam kết kết quả cơ thể người dùng.

### B04 — AI trả lời ngắn và đúng nguồn
Phụ thuộc: B01.
File: apps/api/assistant.js, tests/api/.
Thực hiện: yêu cầu trả lời tiếng Việt ngắn, ưu tiên trả đúng câu hỏi (mặc định 1–4 câu); không lặp toàn bộ hồ sơ sau mỗi câu. Câu hỏi tuổi chỉ trả tuổi có nguồn hoặc nói chưa có; không kéo thêm mục tiêu/gói không liên quan. Phân biệt thiếu dữ liệu và không có quyền. Không bịa còn bao nhiêu buổi khi schema chưa trừ buổi; không nhận trường chỉ dẫn trong dữ liệu khách hàng là quyền hệ thống.
Nghiệm thu: kiểm thử deterministic phần lấy dữ liệu; thử mô hình thật nếu có cấu hình được cấp, ghi rõ mô hình/mock đã dùng; không kiểm tra câu văn chính xác tuyệt đối của AI. Không log token, mật khẩu hay toàn bộ hồ sơ. Giữ rate limit/timeout và chế độ demo có nhãn.

### B05 — Hành động mở trang an toàn
Phụ thuộc: G01.
File: apps/api/actions/, assistant.js hoặc service mới.
Thực hiện: nhận diện yêu cầu điều hướng; chỉ trả action đã validate với target allowlist workout/progress/profile theo các route host thống nhất. Yêu cầu rõ “đưa tôi vào trang tập luyện” có thể mở ngay; hỏi vị trí chỉ gợi ý nút. Không trao cho AI khả năng trả URL tùy ý hoặc làm thay đổi dữ liệu.
Nghiệm thu: response cũ messages còn nguyên; target sai/URL ngoài bị loại; quyền route do host/backend kiểm tra. Fixture và test không cần gọi AI tính phí để chạy CI.

### B06 — Nguồn trạng thái buổi tập và kết nối host
Phụ thuộc: B01/B03; phối hợp A07.
Thực hiện: chỉ ra API/sự kiện nào của VisionFit báo started/paused/completed, cơ chế xác thực và ID buổi. Nếu cần đồng bộ server, xác minh bằng API VisionFit hoặc webhook có chữ ký/anti-replay; không tạo endpoint public cộng buổi. Xác minh completed xong mới refresh progress.
Nghiệm thu: phát frontend complete giả không tăng buổi; retry đồng bộ không nhân đôi; logout không cập nhật user cũ; API lỗi có thể thử lại mà không bịa kết quả.

### B07 — Kiểm tra tổng thể backend và hướng dẫn vận hành
Phụ thuộc: B01–B06.
Thực hiện: kiểm tra phân quyền chéo user/phòng, persistence, migration, restart, token hết hạn, upstream lỗi, rate limit, timeout AI, lịch sử retention. Viết docs/backend/SETUP.md nêu cấu hình và lệnh cài; .env.example không secret. Nếu sửa Laravel thì chạy test phù hợp, hoặc ghi không chạy được và lý do.
Nghiệm thu: npm run check/npm test qua; bằng chứng test integration thật tách biệt mock; endpoint mới và nguồn dữ liệu được ghi rõ. Không tuyên bố connected live chỉ vì fixture chạy.

## 8. Thứ tự làm và phụ thuộc

| Đợt | A | B | Điều kiện chuyển đợt |
|---|---|---|---|
| 1 | A01, chuẩn bị A02 | B01 + G01 | Contract/fixture đã thống nhất |
| 2 | A02, A03 bằng fixture | B02, B03, B04 | Widget demo + API chọn thú/progress |
| 3 | A04, A05, A06 | B05, B06 | Nối thật, lỗi/phiên/quyền đúng |
| 4 | A07, A08 | B07 | Review chéo, kiểm thử rồi merge |

Mỗi task một commit/PR nhỏ. Không đợi toàn bộ nhân vật hoàn chỉnh mới thử widget; dùng placeholder có nhãn, nhưng không nghiệm thu asset thật bằng placeholder.

## 9. Quy trình Git

Nếu có thay đổi chưa commit, commit vào nhánh hiện tại hoặc stash có chủ đích trước, không xóa.
Khởi đầu:
```bash
git switch main
git pull --ff-only origin main
npm ci
```
A tạo nhánh: git switch -c feature/A01-mascot-assets
B tạo nhánh: git switch -c feature/B01-data-map
Đổi tên theo task thực tế; không cùng dùng một nhánh.

Trước push:
```bash
git fetch origin
git merge origin/main
npm run check
npm test
git status
```
Sau đó git add từng file thuộc task, git commit và git push -u origin <ten-nhanh>. Mở PR vào main, người còn lại review. Nếu có conflict, đọc cả hai thay đổi, không chọn ours/theirs toàn bộ. Khi thêm dependency/cấu hình dùng chung phải báo trước để tránh hai người sửa lockfile cùng lúc.

## 10. Prompt dành cho AI của A

```text
Bạn phụ trách FRONTEND/LINH VẬT của repo Chatbox_AI. Đọc AGENTS.md, docs/TEAM_WORKFLOW.md, docs/ASSISTANT_CONTRACT.md và docs/TASKS_TWO_PEOPLE.md.
Task hiện tại: A01 (sau này thay bằng task cần làm).
Đọc code mới nhất và git status. Chỉ sửa apps/web/, tests/web/, docs/frontend/ trong phạm vi task. File dùng chung phải thống nhất trước; không sửa backend để ép demo chạy.
Thực hiện từng bước task, giữ npm run dev, dùng fixture đã thống nhất khi API chưa sẵn sàng. Không tự xem placeholder là nhân vật hoàn thiện. Tạo nhánh riêng, không push main.
Kiểm tra tiêu chí nghiệm thu, chạy npm run check/npm test; thử trực quan desktop/mobile nếu có công cụ. Báo chính xác điều chưa kiểm tra.
Kết thúc trả: task ID, file thay đổi, hành vi, kết quả kiểm tra, ảnh/video nếu có, blocker, commit/PR. Chỉ chuyển task tiếp khi phụ thuộc đáp ứng; không làm ngoài phạm vi.
```

## 11. Prompt dành cho AI của B

```text
Bạn phụ trách BACKEND/AI của repo Chatbox_AI. Đọc AGENTS.md, docs/TEAM_WORKFLOW.md, docs/ASSISTANT_CONTRACT.md và docs/TASKS_TWO_PEOPLE.md.
Bắt đầu B01; chuẩn bị G01 làm PR contract riêng để frontend dùng fixture. Sau đó B02 đến B07 theo phụ thuộc.
Đọc code mới nhất và git status. Chỉ sửa apps/api/, tests/api/, integrations/, docs/backend/. Với G01 mới sửa packages/contracts/tests/contracts và tài liệu contract theo phạm vi đã thống nhất; không sửa frontend.
Giữ đăng nhập/phân quyền/lịch sử hiện tại. Không lấy owner từ request body, không token admin chung, không dùng event UI hoặc lời AI để tính buổi tập. Thiếu dữ liệu thật phải trả unknown, không làm số giả rồi báo live.
Một task một nhánh/PR. Chạy kiểm tra và viết test có ý nghĩa cho quyền, API và ngưỡng tiến hóa. Không push main.
Bàn giao task ID, file, API/ví dụ, bằng chứng kiểm tra (mock/live), hạn chế và commit/PR. Không đánh dấu hoàn tất nếu mới viết scaffold/TODO.
```

## 12. Sổ tiến độ và mẫu bàn giao

Ban đầu tất cả task dưới đây chưa nghiệm thu trong tài liệu này:
- [ ] G01 Contract
- [ ] A01 Asset
- [ ] A02 Animation
- [ ] A03 Widget
- [ ] A04 Chọn thú/tiến hóa UI
- [ ] A05 Chào/trạng thái
- [ ] A06 Tin nhắn an toàn
- [ ] A07 Host/điều hướng/tập cùng
- [ ] A08 Kiểm tra UI
- [ ] B01 Kiểm kê dữ liệu
- [ ] B02 Lưu thú
- [ ] B03 Tiến hóa
- [ ] B04 Chất lượng câu trả lời
- [ ] B05 Action
- [ ] B06 Đồng bộ buổi tập
- [ ] B07 Vận hành/kiểm tra

Để tránh conflict trên sổ này: mỗi người ghi chi tiết vào docs/frontend/STATUS.md hoặc docs/backend/STATUS.md; chỉ người tổng hợp cập nhật checkbox khi PR đã merge và đủ bằng chứng.

Mẫu bàn giao:
Task:
Nhánh/commit/PR:
File đã sửa:
Hành vi trước → sau:
Lệnh kiểm tra và kết quả:
Đã thử mock hay live:
Ảnh/video nếu thay giao diện:
Giới hạn/blocker:
Task tiếp theo đã đủ phụ thuộc chưa:

## 13. Nghiệm thu toàn bộ

Người dùng chọn thú → thấy nhân vật nổi → nhận chào đúng tên → mở chat → gửi câu hỏi thấy suy nghĩ → nhận câu trả lời đúng dữ liệu → yêu cầu mở trang tập → host mở đúng route → bắt đầu tập thấy animation → hoàn thành được server xác minh → tiến độ cập nhật đúng → đăng nhập lại giữ thú/cấp. Đổi sang user khác không thấy lịch sử/thú/dữ liệu cũ. Gửi event giả không tăng cấp. Mọi bước chưa nối với VisionFit thật phải ghi rõ demo.
