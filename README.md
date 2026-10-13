Lab A7 - Xin quyền truy cập tài nguyên (Runtime Permission)

Thông tin sinh viên: Nguyễn Hoài Phương - MSSV 241A010638 - Lớp LTTTBDD - Môn INT4211 Lập trình trên các thiết bị di động - Tuần 7. Package vn.edu.vhu.ltdd.a7permission, ngôn ngữ Java và XML, minSdk 24, template Empty Views Activity, theme Theme.Material3.DayNight.NoActionBar.

Mô tả ngắn gọn

App gồm một màn hình với khung xem trước ảnh, dòng trạng thái quyền và bốn nút. Nút Chụp ảnh xin quyền CAMERA theo đúng mẫu ba nhánh của Google: đã có quyền thì dùng ngay, từng từ chối thì hiện hộp thoại giải thích lý do trước khi hỏi lại, bị chặn vĩnh viễn thì hiện hộp thoại mời mở Cài đặt. Nút Gửi thông báo xin quyền POST_NOTIFICATIONS và chỉ xin từ Android 13, kèm kiểm tra Build.VERSION.SDK_INT cho máy cũ hơn. Nút Mở Cài đặt ứng dụng đưa người dùng đến đúng trang thông tin của app. Dòng trạng thái được cập nhật trong onResume nên khi bật quyền trong Cài đặt rồi quay lại app thì giá trị đổi ngay.

Cấu trúc thư mục chính

app/src/main/java/vn/edu/vhu/ltdd/a7permission/MainActivity.java chứa ba ActivityResultLauncher khai báo ở cấp field và toàn bộ luồng xin quyền
app/src/main/res/layout/activity_main.xml là màn hình duy nhất gồm ImageView, TextView trạng thái và bốn nút
app/src/main/res/values/strings.xml chứa nhãn, lời giải thích quyền, nội dung thông báo và chuỗi trạng thái
app/src/main/AndroidManifest.xml khai báo uses-permission CAMERA và POST_NOTIFICATIONS cùng uses-feature camera required false

Các chức năng đã hoàn thành theo thang điểm

Khai báo Manifest đúng, app cài và chạy được. Xin quyền CAMERA đúng mẫu ba nhánh, có Log.d kết quả từng lần xin. Hộp thoại giải thích hiện khi người dùng đã từ chối một lần, hộp thoại mời mở Cài đặt hiện khi quyền bị chặn vĩnh viễn. Cập nhật trạng thái quyền trong onResume bằng hàm capNhatTrangThai(). POST_NOTIFICATIONS xin có điều kiện phiên bản, tạo NotificationChannel một lần trong onCreate và bọc notify trong try-catch SecurityException. Bảng bảy kịch bản kiểm thử kèm ảnh minh chứng đã chuẩn bị khung nội dung, phần ảnh chụp thao tác trên máy. Hai bài nâng cao NC1 và NC3 đã làm.

Bài nâng cao đã thực hiện

NC1 - Xin hai quyền cùng lúc và xử lý từng quyền riêng. Dùng ActivityResultContracts.RequestMultiplePermissions() thay cho RequestPermission() vì contract này nhận mảng quyền và trả về Map<String, Boolean>. Khai báo launcher thứ ba tên xinHaiQuyen ở cấp field, callback duyệt Map để lấy riêng giá trị của CAMERA và POST_NOTIFICATIONS, sau đó hiện Toast "NC1 – CAMERA: ĐÃ CẤP, THÔNG BÁO: CHƯA CẤP" và ghi log. Thêm nút thứ tư btnXinCaHai trong layout để gọi launcher với mảng hai quyền. Xử lý riêng từng quyền là điểm chính của bài: người dùng có thể cho quyền này nhưng từ chối quyền kia, nếu chỉ kiểm tra một giá trị boolean chung thì sẽ sai.

NC3 - Bấm vào thông báo thì mở lại app. Trong hàm guiThongBao() tạo Intent về MainActivity với cờ FLAG_ACTIVITY_CLEAR_TOP và FLAG_ACTIVITY_SINGLE_TOP, bọc bằng PendingIntent.getActivity với cờ FLAG_IMMUTABLE rồi gắn vào builder bằng setContentIntent(). Cờ FLAG_IMMUTABLE là bắt buộc từ Android 12, nếu thiếu sẽ ném IllegalArgumentException. Kết hợp với setAutoCancel(true) nên bấm vào thông báo vừa mở lại app vừa tự xóa thông báo.

Cách chạy và kiểm thử

Mở project A7_241A010638 trong Android Studio. Trước khi chạy, vào Device Manager, bấm biểu tượng bút của máy ảo, mục Camera đặt Front và Back là Emulated rồi lưu lại, nếu để None thì bấm Chụp ảnh sẽ không có gì xảy ra. Bấm Run app. Muốn thử lại từ trạng thái chưa hỏi bao giờ thì vào Cài đặt, Ứng dụng, app A7, Quyền, Đặt lại quyền. Logcat lọc theo tag A7_241A010638 để xem kết quả xin quyền và dòng trạng thái.

Ghi chú kỹ thuật

Ba ActivityResultLauncher phải khai báo ở cấp field vì registerForActivityResult bắt buộc chạy trước khi Activity đạt trạng thái STARTED, gọi trong onClick sẽ crash với thông báo LifecycleOwner is attempting to register while current state is RESUMED. NotificationChannel sau khi tạo không đổi được importance bằng code, muốn thử lại phải gỡ app cài lại hoặc đổi CHANNEL_ID. TakePicturePreview chỉ trả bitmap khoảng 100 đến 200 pixel đủ để xem trước, cần ảnh gốc thì phải dùng TakePicture kèm FileProvider. Quyền CAMERA là quyền nguy hiểm nên vừa phải khai báo Manifest vừa phải xin lúc chạy, khác quyền thường chỉ cần khai báo là hệ thống tự cấp.
