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
<summary><strong>HTDotFile <code>0.1.0</code></strong> — quản lý dotfile trong terminal</summary>

### Giúp gì?

- Quản lý danh sách dotfile bằng YAML và đường dẫn portable.
- Quét trạng thái file, symlink và bản copy mà không sửa target.
- Thêm preset cho Git, terminal, editor và các AI coding tools.
- Thiết lập repository Git bằng SSH Agent, SSH key hoặc HTTPS.

### Cài đặt

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
htdot
htdot --manifest /path/to/htdot.yaml
htdot --version
```

Hiện tại thao tác apply vẫn được khóa an toàn; tool chưa tạo, xóa, ghi đè, copy hoặc symlink target.

</details>

<a id="aabtoapk"></a>
<details>
<summary><strong>AabToApk <code>0.1.0</code></strong> — chuyển <code>.aab</code> thành universal <code>.apk</code></summary>

### Giúp gì?

- Tạo APK từ Android App Bundle để cài trực tiếp lên device hoặc gửi cho tester.
- Convert một file hoặc cả thư mục, chọn thư mục output và kiểm soát ghi đè.
- Kiểm tra Java, hướng dẫn cài đặt theo hệ điều hành và tải Google bundletool khi máy chưa có.
- Hỗ trợ TUI cho thao tác nhanh và headless cho script/automation.
- Bản Go `0.1.0` đã phát hành trên R2 cho Windows amd64, Linux amd64/arm64 và macOS amd64/arm64; installer kiểm tra SHA256 trước khi cài.

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
aabtoapk install-java   # kiểm tra Java và in hướng dẫn cài đặt an toàn
aabtoapk install        # tải Google bundletool.jar
```

Trong TUI: **Ctrl+J** cài Java, **Ctrl+I** cài bundletool.

### Dùng nhanh

```bash
aabtoapk                         # mở TUI
aabtoapk app-release.aab         # convert một file
aabtoapk ./builds -o ./apks --overwrite
aabtoapk list ./builds           # xem trước các file AAB
aabtoapk app.aab -y              # tự đồng ý cài bundletool nếu thiếu
aabtoapk install-java --force    # cài lại JDK
```

| Lệnh / flag | Tác dụng |
|-------------|----------|
| `path` | File hoặc thư mục chứa `.aab` |
| `-o, --out` | Chọn thư mục output |
| `--overwrite` | Cho phép ghi đè APK đã có |
| `--headless` | Chạy không có TUI |
| `install-java` / **Ctrl+J** | Kiểm tra Java và xem hướng dẫn cài đặt |
| `--install` / `install` / **Ctrl+I** | Cài bundletool |
| `-y, --yes` | Tự động đồng ý prompt |
| `--ks` … | Cấu hình ký APK khi cần |

</details>
