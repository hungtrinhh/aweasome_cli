# Awesome CLI Tools

Bộ CLI nhỏ gọn cho công việc Android hằng ngày. Chọn nhanh tool cần dùng, mở phần chi tiết khi cần cài đặt hoặc xem lệnh mẫu.

| Tool | Dùng khi cần | Điểm chính |
|------|--------------|------------|
| [AttackLogcat](#attacklogcat) | Đọc và lọc log Android nhanh hơn `adb logcat` thuần | Chọn device/package, lọc level/text/regex, TUI hoặc headless |
| [AabToApk](#aabtoapk) | Chuyển file `.aab` thành universal `.apk` để cài/test | Xử lý file hoặc thư mục, tự cài Java và bundletool |
| [HTDotFile](#htdotfile) | Quản lý dotfile kiểu chezmoi | Source/target state, template, mã hoá age/gpg; app Electron ở system tray + CLI, sync Git thủ công |

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
<summary><strong>HTDotFile <code>2.4.0</code></strong> — quản lý dotfile kiểu chezmoi: source/target state, template, mã hoá; có app tray và CLI</summary>

### Giúp gì?

- Mô hình chezmoi: repo Git chứa *source state* với tên file mã hoá thuộc tính (`dot_gitconfig`, `private_dot_ssh/`, `executable_*`, `exact_*`, `symlink_*`, `*.tmpl`, `encrypted_*`); tool tính *target state* cho máy hiện tại rồi áp dụng phần chênh lệch.
- Template cú pháp Go (`{{ if eq .htdot.os "windows" }}`...) với dữ liệu máy (`.htdot.os/arch/hostname/username`), file `.htdotdata.yaml|json`, `.htdotignore` cũng là template; hàm `include`, `env`, `output`, `onepasswordRead`, `bwPassword`...
- Mã hoá secret bằng `age` (thuần JS, không cần cài thêm) hoặc `gpg`: `htdot keygen`, `htdot add --encrypt`.
- CLI tương thích chezmoi v2.73.1: `init`, `add` (cả thư mục, đệ quy), `re-add`, `forget`, `remove`, `chattr`, `edit`, `managed`/`unmanaged`/`ignored`, `status`, `diff`, `apply [-n]`, `verify`, `update`, `merge`, `import`, `archive`, `execute-template`, `dump`, `encrypt`/`decrypt`, `doctor`, `secret`, `state`... cùng các flag của chezmoi. Không có migration từ `htdot.yaml` của bản 1.x — thêm lại file bằng `htdot add`.
- App desktop Electron: danh sách target kèm trạng thái, xem diff, preview rồi apply, kéo-thả file/thư mục để add, nút Re-add/Forget từng dòng; thao tác nặng chạy trong worker thread nên cửa sổ không bị treo; target cũ (hoặc mục thừa trong thư mục `exact_`) được rename vào thư mục backup liền kề, rollback nếu lỗi, không bao giờ xoá.
- Thiết lập repository Git bằng SSH Agent, SSH key hoặc HTTPS/GCM; sync chỉ chạy khi người dùng bấm (không còn sync nền từ 2.1.0): commit/push thay đổi local, fast-forward repo sạch và yêu cầu quyết định rõ ràng khi repo diverged (`Use remote` / `Use local` kèm recovery branch).
- Sống ở system tray: đóng cửa sổ chỉ ẩn đi; menu tray có `Sync now` và `Start with Windows`.

### Cài đặt

Cài qua npm trên Windows, Linux hoặc macOS (Node 22 trở lên):

```bash
npm i -g htdotfile
```

Electron runtime được tải ở lần chạy đầu nếu installer bỏ qua bước này. Git chỉ cần khi đồng bộ repository.

### Dùng nhanh

```bash
htdot                          # mở app (icon xuất hiện ở tray)
htdot --show                   # mở cửa sổ ngay
htdot add ~/.gitconfig         # nhập file vào source (--template, --encrypt)
htdot diff && htdot apply      # xem khác biệt rồi áp dụng (apply -n chỉ xem plan)
htdot update                   # sync repo (pull/push) rồi apply
htdot --config <file> ...      # dùng file settings khác (HTDOT_CONFIG); HTDOT_TARGET đổi thư mục đích
htdot version
```

Chạy `htdot` lần nữa khi app đang chạy sẽ mở lại cửa sổ hiện có. Thoát hẳn bằng `Exit` trong menu tray.

Apply luôn revalidate trước khi ghi: target xuất hiện/đổi/biến mất sau preview sẽ bị từ chối. File được ghi từ nội dung đã render (copy, không symlink về repo); target hiện có được rename vào thư mục backup liền kề, nếu cài đặt lỗi tool tự rollback.

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
