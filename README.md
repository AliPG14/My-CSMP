# My-CSMP
This is a C sharp built in app. Using as a music player and manager.
1. Khối Giao ước (Interfaces)
Đây là các bản hợp đồng ép buộc các lớp khác phải tuân theo, giúp ứng dụng linh hoạt và dễ mở rộng.

IPlayable: Quản lý các hành vi phát nhạc cốt lõi.

Phương thức: Play(), Pause(), Stop(), Seek(double seconds).

IAudioDSP (Bộ xử lý tín hiệu số): Đây là điểm nhấn kỹ thuật của dự án.

Phương thức: ApplyFilter(double[] audioData). Mọi bộ lọc âm thanh đều phải implement hàm này để biến đổi mảng tín hiệu đầu vào.

2. Khối Dữ liệu cốt lõi (Abstract Class & Kế thừa)
Chúng ta sẽ tạo một lớp nền tảng cho mọi file âm thanh, sau đó kế thừa nó ra các định dạng cụ thể.

Lớp trừu tượng MediaFile (Abstract Class):

Fields (private): _filePath (đường dẫn), _fileSize (dung lượng), _sampleRate (tần số lấy mẫu).

Properties (public): Title, Duration (chỉ có get, không cho phép gán bừa bãi từ bên ngoài).

Constructor (Hàm tạo): MediaFile(string filePath). Khi khởi tạo một đối tượng bài hát, tuyệt đối không được truyền những tham số giữ chỗ (placeholder) kiểu như "BaiHat1". Hàm tạo này bắt buộc phải nhận vào một đường dẫn thật, gọi API hệ thống để kiểm tra sự tồn tại của file, và tự động trích xuất metadata thật (tên bài, thời lượng) để gán vào các thuộc tính. Nếu file không tồn tại, lập tức ném ra ngoại lệ FileNotFoundException.

Phương thức trừu tượng: abstract void DecodeAudio();

Các lớp con WavFile và Mp3File:

Kế thừa MediaFile và implement IPlayable.

Tính Đa hình (Polymorphism): Lớp WavFile sẽ override hàm DecodeAudio() để đọc dữ liệu PCM thô, trong khi Mp3File sẽ override hàm này để sử dụng thuật toán giải nén phức tạp. Khi chạy chương trình, bạn chỉ cần gọi media.DecodeAudio(), hệ thống sẽ tự biết cách giải mã đúng định dạng.

3. Khối Xử lý Tín hiệu (DSP)
Thay vì code cứng các hiệu ứng âm thanh, chúng ta dùng Đa hình để xử lý.

Lớp ChebyshevFilter và IIRFilter:

Cả hai đều implement interface IAudioDSP.

Khi người dùng muốn thay đổi chất âm (ví dụ: cắt tần số cao, tăng bass), ứng dụng sẽ truyền luồng dữ liệu bài hát qua hàm ApplyFilter(double[] audioData). Mỗi lớp sẽ áp dụng các phương trình toán học riêng (như hàm truyền đạt của bộ lọc IIR hoặc Chebyshev) để tính toán tần số đáp ứng và trả về tín hiệu đã được làm sạch hoặc biến đổi.

4. Khối Quản lý (Aggregation)
Lớp Playlist:

Thuộc tính: List<MediaFile> _songs, string PlaylistName.

Phương thức: AddSong(MediaFile song), RemoveSong(string filePath), Shuffle(), GetTotalDuration().

Xử lý ngoại lệ (Exception): Trong hàm AddSong, nếu người dùng cố gắng thêm một định dạng file không hỗ trợ (ví dụ file .txt thay vì .mp3), hàm sẽ ném ra InvalidDataException để giao diện bắt lỗi và hiện thông báo đỏ cho người dùng.

Cách ráp nối trong C# (Logic hoạt động):
Khi chương trình chạy, bạn tạo một Playlist. Mỗi khi người dùng bấm "Thêm bài hát" trên giao diện WPF và chọn một file, bạn dùng lệnh new Mp3File(đường_dẫn_thực_tế) để khởi tạo. Nếu đường dẫn chuẩn, đối tượng được tạo thành công và thêm vào danh sách. Khi bấm nút Play, bạn lấy bài hát đó ra, gọi bộ lọc ChebyshevFilter.ApplyFilter() (nếu có bật hiệu ứng), rồi gọi Play().

Với cấu trúc chặt chẽ như thế này, dự án của bạn đã bao quát toàn bộ 100% yêu cầu về mặt kiến trúc Hướng đối tượng của Rubric. Bạn có muốn mình viết nháp thử đoạn code C# cho class MediaFile và Mp3File để xem cách ném ngoại lệ (Exception) và ép buộc khởi tạo dữ liệu thực tế không?
