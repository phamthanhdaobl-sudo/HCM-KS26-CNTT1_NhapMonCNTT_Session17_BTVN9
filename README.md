# HCM-KS26-CNTT1_NhapMonCNTT_Session17_BTVN9
# BÀI 9: THIẾT KẾ KHÔNG GIAN ĐA PROFILE VÀ KIẾN TRÚC BOOKMARK CHO TRẠM KHÁM BỆNH

**Họ và tên:** Phạm Thành Đạo  
**Lớp:** HCM-KS26-CNTT1  
**Môn học:** Nhập môn CNTT  
**Bài:** 9 – Thiết kế không gian đa Profile và kiến trúc Bookmark

---

# PHẦN 1: BÁO CÁO PHÂN TÍCH KIẾN TRÚC ĐA PROFILE

## 1. Phân tích nguyên nhân dẫn đến hiện tượng Session Conflict

### 1.1. Khái niệm User Data Directory

Google Chrome sử dụng một thư mục dữ liệu người dùng (User Data Directory) để lưu trữ thông tin và trạng thái hoạt động của trình duyệt.

Trong một Profile, Chrome quản lý nhiều loại dữ liệu như:

- Cookies: Lưu thông tin phiên đăng nhập và trạng thái xác thực của website.
- History: Lưu lịch sử truy cập website.
- Bookmarks: Lưu các trang web yêu thích.
- Extensions: Lưu các tiện ích mở rộng đã cài đặt.
- Cache: Lưu dữ liệu tạm thời để tăng tốc độ tải trang.
- Local Storage và Session Storage: Lưu dữ liệu do website tạo ra trong trình duyệt.

Mỗi Profile có không gian dữ liệu riêng. Khi nhiều nhân viên sử dụng chung một Profile, các phiên đăng nhập và dữ liệu duyệt web có thể bị sử dụng chung, dẫn đến nguy cơ xung đột.

### 1.2. Nguyên nhân sâu xa của Session Conflict

Trong tình huống tại trạm khám CarePulse, nhiều nhân viên cùng sử dụng một Profile Chrome mặc định.

Nguyên nhân chính:

**Thứ nhất: Dùng chung kho lưu trữ Cookies.**

Các tab và cửa sổ thông thường thuộc cùng một Profile có thể sử dụng chung Cookie Jar. Khi hai nhân viên đăng nhập vào cùng một hệ thống bằng các tài khoản khác nhau, Cookie xác thực có thể bị thay đổi hoặc ghi đè.

Ví dụ:

1. Bác sĩ trực đăng nhập tài khoản CarePulse của mình.
2. Hệ thống cấp Cookie xác thực cho phiên làm việc.
3. Một nhân viên khác mở thêm tab và đăng nhập tài khoản khác trên cùng hệ thống CarePulse.
4. Website có thể cập nhật Cookie hoặc thay đổi phiên xác thực.
5. Phiên làm việc trước đó có thể bị mất hiệu lực và yêu cầu đăng nhập lại.

**Thứ hai: Cơ chế quản lý phiên từ phía máy chủ.**

Một số hệ thống chỉ cho phép một phiên đăng nhập hoạt động tại một thời điểm. Khi tài khoản được đăng nhập ở một thiết bị hoặc phiên mới, máy chủ có thể thu hồi phiên cũ.

**Thứ ba: Dùng chung Local Storage và Session Storage trong cùng môi trường website.**

Dữ liệu phía trình duyệt có thể lưu trạng thái đăng nhập, thông tin cấu hình và các mã định danh phiên. Việc sử dụng chung môi trường trình duyệt có thể gây nhầm lẫn nếu nhiều người cùng thao tác trên một hệ thống.

**Lưu ý:** Việc đăng nhập GitHub hoặc cổng học tập PTIT ở một tab không tự động làm mất phiên CarePulse ở một tên miền khác. Sự cố thường liên quan đến việc dùng chung tài khoản, phiên đăng nhập trên cùng hệ thống, cơ chế SSO hoặc chính sách phiên của máy chủ.

### 1.3. Giải pháp kiến trúc

Sử dụng mô hình đa Profile để tạo các không gian lưu trữ độc lập.

Mỗi Profile có một User Data Directory riêng, giúp tách biệt:

- Cookies và thông tin xác thực.
- History và dữ liệu duyệt web.
- Bookmarks.
- Extensions.
- Local Storage và các dữ liệu website.

Nhờ đó, hoạt động đăng nhập và kiểm thử của một Profile không trực tiếp làm thay đổi dữ liệu phiên của Profile khác.

**Kết luận:** Phân tách Profile là giải pháp hiệu quả để ngăn xung đột phiên do dùng chung dữ liệu trình duyệt. Tuy nhiên, cần kết hợp tài khoản hệ điều hành riêng, phân quyền và chính sách bảo mật để bảo vệ dữ liệu thực sự.

---

## 2. Thiết kế mô hình 3 Profile chuyên dụng cho CarePulse

Nhóm đề xuất triển khai ba Profile gồm Work, Personal và Sandbox/Test.

### 2.1. Work Profile – Môi trường làm việc chuyên môn

**Tên Profile:** CAREPULSE_WORK

**Mục đích sử dụng:**

- Phục vụ bác sĩ trực chính và điều dưỡng viên.
- Truy cập hệ thống CarePulse.
- Tra cứu thông tin bảo hiểm y tế.
- Tra cứu Dược thư Quốc gia.
- Sử dụng các công cụ y tế được đơn vị phê duyệt.

**Màu Theme:** Xanh dương đậm.

Lý do: Màu xanh dương tạo cảm giác chuyên nghiệp, dễ nhận diện và phù hợp với môi trường y tế.

**Dữ liệu lưu trữ độc lập:**

| Loại dữ liệu | Cách quản lý |
|---|---|
| Cookies | Chỉ lưu phiên đăng nhập công việc được cấp quyền |
| History | Chỉ lưu lịch sử truy cập phục vụ chuyên môn |
| Bookmarks | Lưu các cổng y tế và tài liệu chuyên môn |
| Extensions | Chỉ cài tiện ích đã được quản trị viên phê duyệt |
| Cache | Lưu dữ liệu tạm thời, không dùng làm nơi lưu hồ sơ bệnh nhân |

**Quy định:**

- Không đăng nhập tài khoản Facebook, Gmail cá nhân hoặc tài khoản giải trí.
- Không lưu mật khẩu CarePulse trên trình duyệt nếu chưa được đơn vị phê duyệt.
- Phải đăng xuất sau khi hoàn thành ca trực.
- Tự động khóa màn hình khi không có người sử dụng.

### 2.2. Personal Profile – Môi trường cá nhân

**Tên Profile:** PERSONAL_PRIVATE

**Mục đích sử dụng:**

- Phục vụ nhu cầu cá nhân của nhân viên khi được phép.
- Truy cập Gmail, YouTube và các trang web cá nhân.
- Lưu Bookmark phục vụ học tập và giải trí.

**Màu Theme:** Tím.

Lý do: Màu tím giúp phân biệt trực quan với môi trường làm việc chuyên môn.

**Dữ liệu lưu trữ độc lập:**

| Loại dữ liệu | Cách quản lý |
|---|---|
| Cookies | Lưu phiên đăng nhập tài khoản cá nhân |
| History | Lưu lịch sử duyệt web cá nhân |
| Bookmarks | Lưu website cá nhân |
| Extensions | Các tiện ích phục vụ nhu cầu cá nhân |
| Cache | Lưu dữ liệu tạm của website cá nhân |

**Quy định:**

- Không sử dụng Profile này để truy cập hồ sơ bệnh nhân.
- Không đăng nhập tài khoản cá nhân trên máy trạm dùng chung nếu không được cho phép.
- Không bật Cloud Sync tài khoản cá nhân trên máy tính phòng khám.

### 2.3. Sandbox/Test Profile – Môi trường kiểm thử

**Tên Profile:** SANDBOX_TEST

**Mục đích sử dụng:**

- Kiểm thử các chức năng của CarePulse.
- Thử nghiệm giao diện website.
- Kiểm tra trạng thái đăng nhập và đăng xuất.
- Thử nghiệm Cookie, Local Storage và Session Storage.
- Phục vụ sinh viên thực tập và nhân viên kỹ thuật khi được phân quyền.

**Màu Theme:** Cam.

Lý do: Màu cam tạo sự khác biệt rõ ràng và giúp nhận diện đây là môi trường thử nghiệm.

**Dữ liệu lưu trữ độc lập:**

| Loại dữ liệu | Cách quản lý |
|---|---|
| Cookies | Chỉ sử dụng Cookie của tài khoản kiểm thử |
| History | Lưu lịch sử kiểm thử |
| Bookmarks | Lưu tài liệu kỹ thuật và trang kiểm thử |
| Extensions | Chỉ cài tiện ích phục vụ phát triển và kiểm thử |
| Cache | Có thể xóa sau mỗi đợt kiểm thử |

**Quy định:**

- Chỉ sử dụng tài khoản Test được cấp.
- Không sử dụng dữ liệu bệnh nhân thật.
- Không sao chép thông tin hồ sơ bệnh nhân vào môi trường thử nghiệm.
- Xóa dữ liệu kiểm thử sau khi hoàn thành công việc.

### 2.4. Bảng tổng hợp kiến trúc 3 Profile

| Tiêu chí | Work | Personal | Sandbox/Test |
|---|---|---|---|
| Mục đích | Khám chữa bệnh | Cá nhân | Kiểm thử |
| Theme | Xanh dương | Tím | Cam |
| Cookies | Công việc | Cá nhân | Tài khoản Test |
| History | Chuyên môn | Cá nhân | Kiểm thử |
| Bookmarks | Y tế | Cá nhân | Kỹ thuật |
| Extensions | Được phê duyệt | Cá nhân | Phục vụ Test |
| Dữ liệu bệnh nhân | Chỉ khi được cấp quyền | Không | Không dùng dữ liệu thật |

### 2.5. Nguyên tắc triển khai User Data Directory

Mỗi Profile phải có thư mục dữ liệu riêng biệt.

Ví dụ mô hình tổ chức:

```text
Chrome_User_Data/
│
├── Work_Profile/
│   ├── Cookies
│   ├── History
│   ├── Bookmarks
│   └── Extensions
│
├── Personal_Profile/
│   ├── Cookies
│   ├── History
│   ├── Bookmarks
│   └── Extensions
│
└── Sandbox_Profile/
    ├── Cookies
    ├── History
    ├── Bookmarks
    └── Extensions
```

Đây là sơ đồ logic minh họa. Trong thực tế, Chrome sử dụng cấu trúc thư mục và các tệp dữ liệu nội bộ riêng; không nên tự ý tạo hoặc chỉnh sửa các tệp Cookies, History hay Extensions bằng tay.

**Kết quả mong đợi:** Mỗi môi trường có dữ liệu và phiên đăng nhập riêng, hạn chế tối đa tình trạng xung đột phiên giữa các nhân viên.

---

# PHẦN 2: THIẾT KẾ HỆ THỐNG BOOKMARK VÀ QUY TRÌNH AN TOÀN

## 3. Thiết kế cây thư mục Bookmark chuẩn

### 3.1. Mục tiêu

Thanh Bookmark hiện tại có hơn 80 liên kết lộn xộn, gây khó khăn khi tìm kiếm tài liệu.

Nhóm đề xuất tổ chức lại theo ba thư mục chính:

1. 01_CarePulse_Clinic
2. 02_Pharma_Portal
3. 03_Medical_Tools

Việc phân loại giúp bác sĩ và điều dưỡng viên tìm được tài liệu cần thiết nhanh hơn.

### 3.2. Cây thư mục Bookmark đề xuất

```text
BOOKMARK BAR
│
├── 📁 01_CarePulse_Clinic
│   ├── CarePulse - Hệ thống khám bệnh
│   ├── Quản lý hồ sơ bệnh nhân
│   ├── Quản lý lịch khám
│   ├── Kê đơn điện tử
│   ├── Quản lý kết quả xét nghiệm
│   ├── Quản lý lịch trực
│   ├── Tra cứu thông tin bệnh nhân
│   ├── Báo cáo hoạt động khám chữa bệnh
│   ├── Cổng BHYT
│   └── Hướng dẫn sử dụng CarePulse
│
├── 📁 02_Pharma_Portal
│   ├── Dược thư Quốc gia
│   ├── Cục Quản lý Dược
│   ├── Cơ sở dữ liệu thuốc
│   ├── Tra cứu hoạt chất
│   ├── Tra cứu tương tác thuốc
│   ├── Tra cứu chống chỉ định
│   ├── Hướng dẫn sử dụng thuốc
│   ├── Danh mục thuốc thiết yếu WHO
│   ├── Thông tin cảnh giác dược
│   └── Hướng dẫn bảo quản thuốc
│
└── 📁 03_Medical_Tools
    ├── Google Dịch
    ├── PubMed
    ├── WHO
    ├── MedlinePlus
    ├── ICD-10 WHO
    ├── Google Scholar
    ├── ClinicalTrials.gov
    ├── Cochrane Library
    ├── CDC
    └── Từ điển y khoa
```

**Tổng số:** 30 liên kết, chia đều thành 3 thư mục, mỗi thư mục có 10 liên kết.

Lưu ý: Các liên kết nội bộ như CarePulse, quản lý hồ sơ bệnh nhân và kê đơn điện tử phải sử dụng URL chính thức do đơn vị quản trị cấp. Không tự ý sử dụng đường dẫn giả định. Các dịch vụ y tế bên ngoài cũng cần được xác minh trước khi đưa vào Bookmark chính thức.

### 3.3. Cách tạo cây thư mục Bookmark

Thực hiện trên Google Chrome:

**Bước 1:** Mở trình duyệt Chrome.

**Bước 2:** Nhấn tổ hợp phím:

```text
Ctrl + Shift + O
```

Trên macOS sử dụng:

```text
Command + Option + B
```

**Bước 3:** Mở Trình quản lý dấu trang (Bookmark Manager).

**Bước 4:** Chọn dấu ba chấm và tạo ba thư mục chính:

- 01_CarePulse_Clinic
- 02_Pharma_Portal
- 03_Medical_Tools

**Bước 5:** Thêm các liên kết vào từng thư mục tương ứng.

**Bước 6:** Sắp xếp các liên kết theo mức độ sử dụng thường xuyên để thuận tiện khi thao tác.

**Kết quả:** Hệ thống Bookmark được tổ chức thành ba nhóm rõ ràng, giúp giảm thời gian tìm kiếm và hạn chế nhầm lẫn.

---

## 4. Áp dụng kỹ thuật Favicon-Only Bookmark

### 4.1. Khái niệm

Favicon-Only Bookmark là kỹ thuật chỉ hiển thị biểu tượng của website trên thanh Bookmark bằng cách xóa phần tên hiển thị của liên kết.

Thông thường, Bookmark gồm hai thành phần:

- Favicon: Biểu tượng nhận diện website.
- Tên Bookmark: Phần văn bản mô tả website.

Khi xóa tên Bookmark nhưng giữ nguyên liên kết, trình duyệt chỉ hiển thị biểu tượng Favicon.

### 4.2. Bốn website áp dụng

| Website | Tên hiển thị ban đầu | Sau khi áp dụng |
|---|---|---|
| CarePulse | CarePulse - Hệ thống khám bệnh | Chỉ còn Favicon |
| Cổng BHYT | Cổng tra cứu bảo hiểm y tế | Chỉ còn Favicon |
| Dược thư | Dược thư Quốc gia | Chỉ còn Favicon |
| Google Dịch | Google Translate | Chỉ còn Favicon |

### 4.3. Các bước thực hiện

**Bước 1:** Mở website cần lưu Bookmark.

**Bước 2:** Nhấn tổ hợp phím:

```text
Ctrl + D
```

Trên macOS:

```text
Command + D
```

**Bước 3:** Đặt tên Bookmark tạm thời và lưu vào Bookmark Bar.

**Bước 4:** Nhấp chuột phải vào Bookmark vừa tạo.

**Bước 5:** Chọn Edit (Chỉnh sửa).

**Bước 6:** Xóa toàn bộ nội dung trong ô Name (Tên).

**Bước 7:** Nhấn Save để lưu lại.

Sau khi hoàn thành, Bookmark chỉ còn biểu tượng Favicon.

### 4.4. Lợi ích

- Tiết kiệm diện tích thanh Bookmark.
- Có thể hiển thị nhiều website hơn trên cùng một hàng.
- Nhận diện nhanh những website đã quen thuộc.
- Giảm sự lộn xộn của thanh công cụ.

**Lưu ý:** Không nên xóa tên của tất cả Bookmark. Chỉ áp dụng với bốn website thường xuyên sử dụng và có Favicon dễ nhận diện để tránh nhầm lẫn.

---

## 5. Xử lý tình huống an ninh khi có khách mượn máy tính

### 5.1. Lựa chọn Guest Profile hay Incognito?

**Đề xuất: Sử dụng Guest Profile (Hồ sơ khách).**

Khi đoàn thanh tra hoặc khách cần mượn máy tính trong khoảng 30 phút, nhân viên phòng khám nên sử dụng Guest Profile thay vì Incognito Mode.

### 5.2. So sánh Guest Profile và Incognito

| Tiêu chí | Guest Profile | Incognito Mode |
|---|---|---|
| Mục đích | Cho người khác sử dụng trình duyệt tạm thời | Duyệt web riêng tư trong phiên tạm thời |
| Dữ liệu Profile chính | Không sử dụng dữ liệu Profile chính | Tách biệt với cửa sổ thường |
| Cookies | Dữ liệu khách được giữ trong phiên | Dữ liệu ẩn danh được giữ trong phiên |
| History | Không lưu lịch sử vào Profile chính | Không lưu lịch sử vào Profile thông thường |
| Kết thúc phiên | Dữ liệu phiên khách bị xóa | Dữ liệu phiên ẩn danh bị xóa |
| Tài khoản cá nhân | Không đăng nhập vào Chrome để đồng bộ | Không sử dụng Profile cá nhân |
| Phù hợp cho khách mượn máy | Rất phù hợp | Phù hợp cho nhu cầu duyệt riêng tư tạm thời |

### 5.3. Quy trình xử lý khi có khách mượn máy 30 phút

**Bước 1: Chuẩn bị máy tính**

- Bác sĩ hoàn thành hoặc tạm dừng công việc trên CarePulse theo quy trình an toàn.
- Đăng xuất khỏi hệ thống khám bệnh.
- Đóng các tab chứa thông tin bệnh nhân.
- Khóa màn hình nếu cần rời máy trong lúc chuẩn bị.

**Bước 2: Mở Guest Profile**

- Nhấn biểu tượng Profile trên Chrome.
- Chọn Guest (Khách).
- Chờ Chrome mở cửa sổ khách mới.

**Bước 3: Cho khách sử dụng**

- Chỉ cho phép truy cập các tài liệu cần thiết.
- Không cung cấp mật khẩu tài khoản chuyên môn.
- Không để khách truy cập hồ sơ bệnh nhân.
- Không cho phép cài đặt tiện ích hoặc thay đổi cấu hình bảo mật.

**Bước 4: Kết thúc phiên**

- Đóng toàn bộ cửa sổ Guest.
- Kiểm tra Guest đã kết thúc.
- Đăng nhập lại bằng tài khoản nhân viên được phân quyền khi tiếp tục công việc.

### 5.4. Cảnh báo nguy cơ khi bật Cloud Sync tài khoản cá nhân

Cloud Sync là tính năng đồng bộ dữ liệu trình duyệt giữa các thiết bị thông qua tài khoản Google.

Nếu nhân viên bật Cloud Sync tài khoản cá nhân trên máy tính phòng khám, có thể phát sinh các rủi ro sau:

**Rủi ro 1: Rò rỉ dữ liệu cá nhân.**

Bookmark, lịch sử và các thông tin trình duyệt có thể đồng bộ lên tài khoản cá nhân, làm tăng nguy cơ lộ thông tin khi máy tính có nhiều người sử dụng.

**Rủi ro 2: Đồng bộ nhầm dữ liệu.**

Các Bookmark và tiện ích cá nhân có thể xuất hiện trên máy trạm y tế, gây lẫn lộn giữa môi trường cá nhân và công việc.

**Rủi ro 3: Mất quyền kiểm soát dữ liệu.**

Nếu tài khoản cá nhân được đăng nhập và lưu trên máy tính dùng chung, người sử dụng sau có thể tiếp cận các dữ liệu còn tồn tại trong Profile nếu không có biện pháp bảo vệ phù hợp.

**Rủi ro 4: Nguy cơ rò rỉ thông tin bệnh nhân.**

Nếu dữ liệu bệnh nhân bị tải xuống, lưu vào trình duyệt hoặc đồng bộ qua một dịch vụ cá nhân không được phê duyệt, thông tin y tế có thể bị truy cập trái phép.

### 5.5. Quy tắc Cloud Sync an toàn

Nhóm đề xuất:

1. Không đăng nhập tài khoản Google cá nhân vào Chrome trên máy trạm phòng khám.
2. Không bật Cloud Sync cá nhân.
3. Chỉ sử dụng tài khoản đồng bộ do đơn vị quản trị cấp và cho phép.
4. Không đồng bộ dữ liệu bệnh nhân, mật khẩu hoặc thông tin nhạy cảm lên dịch vụ cá nhân.
5. Kiểm soát quyền truy cập và thiết lập xác thực nhiều lớp cho tài khoản công việc.
6. Ưu tiên lưu trữ Bookmark chuyên môn trên hệ thống được đơn vị phê duyệt.

---

## 6. Xử lý ngoại lệ khi phòng khám mất kết nối Internet

Khi máy tính phòng khám mất Internet, cần bảo đảm an toàn cho dữ liệu và không làm gián đoạn quy trình chuyên môn ngoài phạm vi cho phép.

### 6.1. Nguy cơ

- Không truy cập được hệ thống CarePulse trực tuyến.
- Không đồng bộ được dữ liệu Bookmark mới.
- Không thể tra cứu thông tin thuốc từ các website trực tuyến.
- Có thể phát sinh việc nhân viên tải dữ liệu bệnh nhân về máy để sử dụng tạm thời.

### 6.2. Giải pháp

**Thứ nhất: Duy trì Bookmark cục bộ.**

Bookmark đã lưu trong Profile vẫn có thể hiển thị khi mất Internet. Tuy nhiên, việc lưu Bookmark không có nghĩa website vẫn truy cập được khi mất mạng.

**Thứ hai: Sử dụng tài liệu ngoại tuyến được phê duyệt.**

Phòng khám cần chuẩn bị các tài liệu chuyên môn cần thiết ở dạng ngoại tuyến, có kiểm soát phiên bản và nguồn cập nhật.

**Thứ ba: Không tự ý lưu hồ sơ bệnh nhân.**

Không tải hoặc sao chép dữ liệu bệnh nhân sang USB, máy tính cá nhân hoặc dịch vụ lưu trữ không được phép.

**Thứ tư: Thực hiện quy trình dự phòng chuyên môn.**

Khi CarePulse không truy cập được, nhân viên thực hiện quy trình dự phòng do đơn vị y tế ban hành và cập nhật lại dữ liệu khi hệ thống hoạt động trở lại.

**Thứ năm: Bảo vệ máy tính khi mất mạng.**

- Không tắt các cơ chế khóa màn hình.
- Không chia sẻ tài khoản giữa nhân viên.
- Không mở rộng quyền truy cập trái phép.
- Kiểm tra và xóa dữ liệu tạm theo quy định của đơn vị.

---

# 7. ĐÁNH GIÁ HIỆU QUẢ VÀ KẾT LUẬN

### 7.1. Đánh giá hiệu quả

Sau khi triển khai mô hình đa Profile và hệ thống Bookmark, CarePulse có thể đạt được các lợi ích:

| Tiêu chí | Trước khi triển khai | Sau khi triển khai |
|---|---|---|
| Quản lý tài khoản | Dùng chung một Profile | Phân tách ba Profile |
| Xung đột phiên | Có nguy cơ cao | Giảm đáng kể nhờ cách ly dữ liệu |
| Quản lý Bookmark | Hơn 80 liên kết lộn xộn | Phân thành ba thư mục khoa học |
| Tìm kiếm tài liệu | Mất nhiều thời gian | Truy cập nhanh theo nhóm |
| Quyền riêng tư | Dễ lộ dữ liệu giữa người dùng | Có phân tách môi trường |
| Kiểm thử | Dùng chung môi trường thật | Có Sandbox riêng |
| Khách mượn máy | Có nguy cơ truy cập dữ liệu cũ | Sử dụng Guest Profile |

### 7.2. Kết luận

Qua bài tập, có thể thấy việc sử dụng chung một Profile trình duyệt trong môi trường y tế tiềm ẩn nhiều nguy cơ về xung đột phiên đăng nhập, mất quyền riêng tư và rò rỉ dữ liệu.

Việc triển khai ba Profile Work, Personal và Sandbox/Test giúp phân tách môi trường làm việc, hạn chế xung đột dữ liệu và hỗ trợ quản lý tài khoản hiệu quả hơn.

Bên cạnh đó, hệ thống Bookmark được tổ chức thành ba thư mục chính với tối thiểu 30 liên kết giúp nhân viên y tế tra cứu nhanh chóng. Kỹ thuật Favicon-Only góp phần tiết kiệm không gian thanh công cụ.

Đối với tình huống khách mượn máy, Guest Profile là lựa chọn phù hợp vì tạo phiên sử dụng tạm thời và xóa dữ liệu phiên sau khi kết thúc.

**Đề xuất cuối cùng:** CarePulse cần kết hợp mô hình đa Profile, Guest Profile, phân quyền tài khoản hệ điều hành, kiểm soát Cloud Sync và quy trình bảo mật dữ liệu bệnh nhân để xây dựng môi trường làm việc an toàn, khoa học và hiệu quả.

---

**TÀI LIỆU THAM KHẢO**

1. Google Chrome Help – Quản lý nhiều hồ sơ Chrome (Chrome Profiles).
2. Google Chrome Help – Duyệt web ở chế độ ẩn danh.
3. Google Chrome Help – Duyệt web với tư cách khách (Guest Mode).
4. Google Chrome Help – Đồng bộ dữ liệu trình duyệt (Sync).
5. World Health Organization (WHO) – Tài liệu và hướng dẫn y tế.
6. National Library of Medicine – PubMed và MedlinePlus.
