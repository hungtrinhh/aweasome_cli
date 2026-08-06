# Awesome CLI Tools

Bộ CLI nhỏ gọn cho công việc Android hằng ngày. Chọn nhanh tool cần dùng, mở phần chi tiết khi cần cài đặt hoặc xem lệnh mẫu.

| Tool | Dùng khi cần | Điểm chính |
|------|--------------|------------|
| [AttackLogcat](#attacklogcat) | Đọc và lọc log Android nhanh hơn `adb logcat` thuần | Chọn device/package, lọc level/text/regex, TUI hoặc headless |
| [AabToApk](#aabtoapk) | Chuyển file `.aab` thành universal `.apk` để cài/test | Xử lý file hoặc thư mục, tự cài Java và bundletool |
| [HTDotFile](#htdotfile) | Quản lý dotfile bằng manifest portable | TUI, preset, quét trạng thái và thiết lập Git repository |

<a id="attacklogcat"></a>
<details>
<summary><strong>AttackLogcat <code>0.1.1</code></strong> — xem, lọc và theo dõi log Android</summary>

### Giúp gì?

- Tìm lỗi theo package, level, tag, text hoặc regex mà không phải nhớ lệnh `adb logcat` dài.
- Chọn device và package trực tiếp trong TUI; hỗ trợ pause và copy log khi debug.
- Chạy headless để stream log ra terminal, pipe sang tool khác hoặc dùng trong script/CI.

### Cài đặt

```powershell
# Windows
irm https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/attacklogcat/install.ps1 | iex
```

```bash
# Linux / macOS
curl -fsSL https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/attacklogcat/install.sh | bash
```

### Dùng nhanh

```bash
attacklogcat                                    # TUI: chọn device + package
attacklogcat -s <serial> -p <package>           # theo dõi một package
attacklogcat --headless --all --level E         # stream mọi lỗi ra stdout
attacklogcat -p com.example.app --regex "Error" # lọc bằng regex
```

| Flag | Tác dụng |
|------|----------|
| `-p, --package` | Chỉ theo dõi package được chọn |
| `-s, --serial` | Chọn device theo serial |
| `--level` | Đặt level tối thiểu: `V/D/I/W/E/F` |
| `--grep` / `--regex` / `--tag` | Lọc log theo text, regex hoặc tag |
| `--headless` | Chạy không có TUI |
| `--all` | Hiển thị log của mọi process |

</details>

<a id="htdotfile"></a>
<details>
<summary><strong>HTDotFile <code>0.3.4</code></strong> — quản lý dotfile trong terminal</summary>

### Giúp gì?

- Quản lý danh sách dotfile bằng YAML và đường dẫn portable.
- Quét, preview và apply file/symlink với backup tự động trước khi thay target.
- Thêm preset cho Git, terminal, editor và các AI coding tools.
- Thiết lập repository Git bằng SSH Agent, SSH key hoặc HTTPS.

### Cài đặt

Cài qua npm trên mọi nền tảng (Node.js 18+):

```bash
npm i -g htdotfile
```

Ngoài ra cũng có installer binary (yêu cầu Node để chạy bản JS, hoặc dùng binary Go qua R2):

```powershell
# Windows
irm https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/htdot/install.ps1 | iex
```

```bash
# Linux / macOS
curl -fsSL https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/htdot/install.sh | bash
```

Installer kiểm tra SHA256, tự chọn binary theo hệ điều hành và cài Git khi máy có package manager được hỗ trợ.

### Dùng nhanh

```bash
htdotfile
htdotfile --manifest /path/to/htdot.yaml
htdotfile --version
```

Apply yêu cầu preview và xác nhận rõ ràng. Target hiện có được rename vào thư mục backup liền kề; nếu cài đặt lỗi, tool tự rollback. Chiến lược `copy` hiện chỉ hỗ trợ file thường, còn thư mục dùng `symlink`.

</details>

<a id="aabtoapk"></a>
<details>
<summary><strong>AabToApk <code>0.1.0</code></strong> — chuyển <code>.aab</code> thành universal <code>.apk</code></summary>

### Giúp gì?

- Convert một file `.aab` hoặc cả thư mục (đệ quy) thành universal APK bằng Google **bundletool** (`build-apks --mode=universal`).
- TUI tương tác (path, options, tiến trình, kết quả) hoặc headless cho script/CI.
- Tự phát hiện Java và `bundletool.jar`; tải bundletool mới nhất vào `~/.aabtoapk` khi cần.
- Hỗ trợ ký APK qua `--ks` và nhấp đúp trên Windows mở console thật.
- Bản Go `0.1.0` phát hành trên R2 cho Windows amd64, Linux amd64/arm64 và macOS amd64/arm64; installer kiểm tra SHA256 rồi tự mở TUI có màu trên Windows.

### Cài đặt

```powershell
# Windows
irm https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/aabtoapk/install.ps1 | iex
```

```bash
# Linux / macOS
curl -fsSL https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/aabtoapk/install.sh | bash
```

Thiết lập dependency lần đầu nếu cần:

```bash
aabtoapk install-java   # quét Java; tự cài Temurin 21 bằng winget nếu thiếu
aabtoapk install        # tải Google bundletool.jar vào ~/.aabtoapk
```

TUI dùng theme GrokNight, badge màu cho trạng thái Java/bundletool và highlight các mục **Install Java**, **Install bundletool** để chọn bằng phím mũi tên, Enter hoặc chuột.

### Dùng nhanh

```bash
aabtoapk                         # mở TUI
aabtoapk app-release.aab         # convert một file
aabtoapk ./builds -o ./apks --overwrite
aabtoapk list ./builds           # xem trước các file AAB (name, package, version, size)
aabtoapk app.aab -y              # tự đồng ý cài bundletool nếu thiếu
aabtoapk install-java --force    # cài lại JDK
```

| Lệnh / flag | Tác dụng |
|-------------|----------|
| `path` | File hoặc thư mục chứa `.aab` |
| `-o, --out` | Thư mục output (mặc định: cạnh file AAB) |
| `--overwrite` | Cho phép ghi đè APK đã có |
| `--bundletool` | Đường dẫn đến `bundletool.jar` |
| `--java` | Đường dẫn đến `java` |
| `--recursive` / `--no-recursive` | Quét thư mục con (mặc định recursive) |
| `--keep-apks` | Giữ file `.apks` trung gian |
| `--headless` | Chạy không có TUI |
| `install` / `--install` | Cài bundletool.jar vào cache |
| `--force-install` | Tải lại dù đã có |
| `install-java` / **Ctrl+J** | Kiểm tra Java và tự cài Temurin 21 nếu thiếu |
| `-y, --yes` | Tự động đồng ý prompt |
| `--ks` … | Cấu hình ký APK khi cần |
| `list <path>` / `ls` / `info` | Xem trước các file AAB |
| `--version` | In version |

</details>
