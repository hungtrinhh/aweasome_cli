# Awesome CLI Tools

## AttackLogcat `0.1.1`

Go TUI/CLI xem `adb logcat` — chọn device/package, filter level/text/regex, pause & copy.

**Install**

```powershell
# Windows
irm https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/attacklogcat/install.ps1 | iex
```

```bash
# Linux / macOS
curl -fsSL https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/attacklogcat/install.sh | bash
```

**Usage**

```bash
attacklogcat                                    # TUI: pick device + package
attacklogcat -s <serial> -p <package>           # gắn package
attacklogcat --headless --all --level E         # stream lỗi ra stdout
attacklogcat -p com.example.app --regex "Error" # filter regex
```

| Flag | Ý nghĩa |
|------|---------|
| `-p, --package` | Package name |
| `-s, --serial` | Device serial |
| `--level` | Min level `V/D/I/W/E/F` |
| `--grep` / `--regex` / `--tag` | Filter |
| `--headless` | Không TUI |
| `--all` | Mọi process |

---

## AabToApk `0.1.0`

Go TUI/CLI convert `.aab` → universal `.apk` (Google bundletool). Cần Java.

**Install**

```powershell
# Windows
irm https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/aabtoapk/install.ps1 | iex
```

```bash
# Linux / macOS
curl -fsSL https://pub-e1c1dbe5b3fc48c4bf1443041724f542.r2.dev/aabtoapk/install.sh | bash
```

```bash
aabtoapk install   # lần đầu: tải bundletool.jar
```

**Usage**

```bash
aabtoapk                         # TUI
aabtoapk app-release.aab         # 1 file
aabtoapk ./builds -o ./apks --overwrite
aabtoapk list ./builds           # preview AAB
aabtoapk app.aab -y              # convert, auto-yes install jar
```

| Flag | Ý nghĩa |
|------|---------|
| `path` | File/folder `.aab` |
| `-o, --out` | Thư mục output |
| `--overwrite` | Ghi đè APK |
| `--headless` | Không TUI |
| `--install` / `install` | Cài bundletool |
| `-y, --yes` | Auto-yes prompt |
| `--ks` … | Ký APK (tuỳ chọn) |
