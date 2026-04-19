# 🚀 SETUP GUIDE — Nick's GitHub Profile README

> Hướng dẫn đầy đủ để deploy toàn bộ README với Interactive Terminal, 3D animations, GitHub Actions.

---

## 📋 Mục lục

1. [Cấu trúc file](#1-cấu-trúc-file)
2. [Deploy Terminal lên Vercel](#2-deploy-terminal-lên-vercel)
3. [Cập nhật URL vào README](#3-cập-nhật-url-vào-readme)
4. [Tạo GitHub Profile Repo](#4-tạo-github-profile-repo)
5. [Setup GitHub Actions](#5-setup-github-actions)
6. [GitHub Secrets cần tạo](#6-github-secrets-cần-tạo)
7. [Chạy lần đầu (manual trigger)](#7-chạy-lần-đầu-manual-trigger)
8. [Kiểm tra kết quả](#8-kiểm-tra-kết-quả)
9. [Tuỳ chỉnh thêm](#9-tuỳ-chỉnh-thêm)
10. [Troubleshooting](#10-troubleshooting)

---

## 1. Cấu trúc file

Sau khi setup xong, repo của bạn sẽ trông như thế này:

```
NgoHuuLoc0612/              ← tên repo = tên GitHub username
├── README.md               ← file chính hiển thị trên profile
├── terminal.html           ← source terminal (deploy lên Vercel)
├── metrics.svg             ← tự sinh bởi GitHub Actions
├── SETUP.md                ← file này
└── .github/
    └── workflows/
        ├── snake.yml           ← sinh snake animation
        ├── 3d-contrib.yml      ← sinh 3D contribution graph
        ├── metrics.yml         ← sinh live metrics card
        └── update-readme.yml   ← auto-update timestamp
```

---

## 2. Deploy Terminal lên Vercel

Terminal dùng JavaScript thuần, GitHub không cho chạy JS trong README.
Giải pháp: **deploy `terminal.html` lên Vercel miễn phí** rồi nhúng link vào README.

### Cách A — Dùng Vercel CLI (nhanh nhất)

```bash
# Bước 1: Cài Vercel CLI
npm install -g vercel

# Bước 2: Di chuyển vào thư mục chứa terminal.html
cd /đường/dẫn/tới/thư/mục

# Bước 3: Login
vercel login

# Bước 4: Deploy
vercel --name nick-terminal

# Trả lời các câu hỏi:
#   Set up and deploy? → Y
#   Which scope?       → chọn account của bạn
#   Link to existing?  → N
#   Project name?      → nick-terminal
#   Directory?         → ./
#   Override settings? → N
```

### Cách B — Kéo thả trên Web (không cần cài gì)

1. Vào [vercel.com](https://vercel.com) → Đăng ký bằng GitHub
2. Vào [vercel.com/new](https://vercel.com/new) → kéo thả file `terminal.html` vào ô upload
3. Vercel tự deploy và sinh URL

### Kết quả

```
URL dạng: https://nick-terminal-abc123.vercel.app
```

> 💡 Có thể đặt tên miền đẹp hơn trong Vercel Dashboard → Settings → Domains  
> Ví dụ: `nick-terminal.vercel.app`

---

## 3. Cập nhật URL vào README

Sau khi có URL từ Vercel, thay tất cả `YOUR_VERCEL_URL` trong `README.md`:

```bash
# Linux / Mac
sed -i 's|YOUR_VERCEL_URL|nick-terminal-abc123.vercel.app|g' README.md

# Windows PowerShell
(Get-Content README.md) -replace 'YOUR_VERCEL_URL','nick-terminal-abc123.vercel.app' | Set-Content README.md
```

Có **4 chỗ** cần thay trong README:

| Vị trí | Nội dung |
|--------|----------|
| Badge "OPEN TERMINAL" | `href="https://YOUR_VERCEL_URL"` |
| Link ảnh preview | `href="https://YOUR_VERCEL_URL"` |
| Image src | `src="https://YOUR_VERCEL_URL/og-preview.png"` |
| Badge cuối trang | `)](https://YOUR_VERCEL_URL)` |

---

## 4. Tạo GitHub Profile Repo

GitHub profile README hoạt động khi có repo tên **trùng với username**.

```
Username:   NgoHuuLoc0612
Tên repo:   NgoHuuLoc0612   ← phải giống hệt username
```

### Các bước:

1. Vào [github.com/new](https://github.com/new)
2. **Repository name:** `NgoHuuLoc0612`
3. Chọn **Public**
4. **Không** tick "Add a README file"
5. Click **Create repository**

### Upload files lên repo:

```bash
git clone https://github.com/NgoHuuLoc0612/NgoHuuLoc0612.git
cd NgoHuuLoc0612

# Copy file vào
cp /đường/dẫn/README.md .
cp /đường/dẫn/terminal.html .
cp /đường/dẫn/SETUP.md .

# Tạo thư mục workflow
mkdir -p .github/workflows
cp /đường/dẫn/.github/workflows/*.yml .github/workflows/

# Push
git add .
git commit -m "feat: most beautiful GitHub profile ✨"
git push origin main
```

---

## 5. Setup GitHub Actions

Tất cả 4 workflow files đã có sẵn — chỉ cần push lên là tự chạy.

| File | Lịch chạy | Tác dụng | Output |
|------|-----------|----------|--------|
| `snake.yml` | Mỗi 12h | Sinh snake animation | `output/github-snake*.svg` |
| `3d-contrib.yml` | Mỗi ngày | Sinh 3D isometric graph | `output/profile-3d-contrib/` |
| `metrics.yml` | Mỗi 6h | Sinh live metrics card | `main/metrics.svg` |
| `update-readme.yml` | Mỗi ngày | Auto-update timestamp | `main/README.md` |

---

## 6. GitHub Secrets cần tạo

Vào: **Repo → Settings → Secrets and variables → Actions → New repository secret**

| Secret Name | Giá trị | Dùng cho |
|-------------|---------|----------|
| `GITHUB_TOKEN` | Tự động có sẵn ✅ | Snake, 3D, update-readme |
| `METRICS_TOKEN` | Tạo thủ công | Metrics workflow |

### Tạo `METRICS_TOKEN`:

1. Vào [github.com/settings/tokens](https://github.com/settings/tokens)
2. **Generate new token (classic)**
3. Note: `METRICS_TOKEN` | Expiration: No expiration
4. Tick scopes: ✅ `read:user` · ✅ `repo` · ✅ `read:org`
5. Generate → Copy token ngay!
6. Dán vào secret `METRICS_TOKEN` trong repo settings

### Bật quyền write cho Actions:

Repo → **Settings → Actions → General → Workflow permissions**
→ Chọn **"Read and write permissions"** → Save

---

## 7. Chạy lần đầu (manual trigger)

Chạy thủ công để không phải chờ schedule:

1. Vào repo → tab **Actions**
2. Chọn từng workflow → **"Run workflow"** → **"Run workflow"**

**Thứ tự khuyến nghị:**

```
1. 🐍 Generate Snake Animation       (~1 phút)
2. 🌐 Generate 3D Contribution Graph (~2 phút)
3. ⚡ Generate GitHub Metrics        (~2 phút)
4. 🔄 Auto Update README             (~30 giây)
```

Sau khi xong → F5 trang `github.com/NgoHuuLoc0612` để xem kết quả!

---

## 8. Kiểm tra kết quả

Truy cập `https://github.com/NgoHuuLoc0612` và kiểm tra:

- ✅ Header venom animation với tên + typing effect
- ✅ Badge "OPEN INTERACTIVE TERMINAL" → link Vercel hoạt động
- ✅ Terminal gõ được, matrix animation, tab auto-complete
- ✅ GitHub stats + streak cards
- ✅ Snake animation đang bò
- ✅ 3D contribution graph rainbow
- ✅ Trophy wall
- ✅ Tech stack badges (C, C++, Python...)
- ✅ Activity graph timeline
- ✅ Mermaid mindmap
- ✅ Live metrics SVG

---

## 9. Tuỳ chỉnh thêm

### Thay username hàng loạt

```bash
# Thay NgoHuuLoc0612 → username của bạn
grep -rl "NgoHuuLoc0612" . | xargs sed -i 's/NgoHuuLoc0612/YOUR_USERNAME/g'

# Thay Twitter handle
grep -rl "benhan871986" . | xargs sed -i 's/benhan871986/YOUR_TWITTER/g'
```

### Đổi màu accent (hiện tại: `#00d4ff` cyan)

```bash
# Đổi sang tím
sed -i 's/00d4ff/a855f7/g' README.md terminal.html

# Đổi sang xanh lá
sed -i 's/00d4ff/22c55e/g' README.md terminal.html

# Đổi sang cam
sed -i 's/00d4ff/f97316/g' README.md terminal.html
```

### Thêm lệnh mới vào Terminal

Mở `terminal.html`, tìm object `CMDS` và thêm:

```javascript
// Thêm vào trong object CMDS
education: () => [
  ``,
  `  <span class="ca">▸  Education</span>`,
  ``,
  `  <span class="cw">University XYZ</span>`,
  `  <span class="cd">   Major  :</span> Computer Science`,
  `  <span class="cd">   Year   :</span> 2020 — 2024`,
  ``,
],
```

Thêm vào `HINT_CMDS` để hiện ở thanh gợi ý:

```javascript
const HINT_CMDS = ['help','whoami','skills','projects','contact','neofetch','matrix','ls','clear','education'];
```

Re-deploy lên Vercel:
```bash
vercel --prod
```

---

## 10. Troubleshooting

### ❌ Snake SVG không hiện (ảnh bị 404)

Branch `output` chưa được tạo.

```
Giải pháp:
1. Actions → "Generate Snake Animation" → "Run workflow"
2. Chờ 2 phút → kiểm tra branch "output" đã xuất hiện chưa
```

### ❌ 3D graph trống / không load

Tài khoản mới chưa có contribution history.

```
Giải pháp:
1. Push một vài commit để có data
2. Chạy lại workflow "Generate 3D Contribution Graph"
```

### ❌ metrics.svg lỗi hoặc không hiện

Token sai hoặc hết hạn.

```
Giải pháp:
1. Tạo lại token tại github.com/settings/tokens
2. Cập nhật secret METRICS_TOKEN trong repo settings
3. Chạy lại "Generate GitHub Metrics"
```

### ❌ Terminal Vercel báo lỗi 404

URL sai hoặc deployment thất bại.

```
Giải pháp:
1. Vào vercel.com/dashboard → kiểm tra deployment status
2. Nếu failed → xem logs → fix lỗi → redeploy
3. Redeploy: vercel --prod (trong thư mục có terminal.html)
```

### ❌ Actions không có quyền commit (permission denied)

```
Giải pháp:
Repo → Settings → Actions → General
→ Workflow permissions → "Read and write permissions" → Save
```

### ❌ README không tự update

Schedule chỉ chạy khi repo có activity gần đây. GitHub tạm dừng Actions của repo không active.

```
Giải pháp:
1. Push bất kỳ commit nhỏ nào để kích hoạt lại
2. Hoặc chạy thủ công "Run workflow" mỗi lần cần
```

---

## 📊 Checklist cuối

```
□ 1. Tạo repo NgoHuuLoc0612/NgoHuuLoc0612 (Public)
□ 2. Deploy terminal.html lên Vercel → lấy URL
□ 3. Thay YOUR_VERCEL_URL trong README.md (4 chỗ)
□ 4. Upload tất cả file lên repo
□ 5. Tạo METRICS_TOKEN → thêm vào repo secrets
□ 6. Bật "Read and write permissions" cho Actions
□ 7. Chạy thủ công 4 workflows theo thứ tự
□ 8. Kiểm tra github.com/NgoHuuLoc0612
□ 9. Flex với mọi người 😎
□ 10. 🎉 Done — profile đẹp nhất GitHub!
```

---

<div align="center">

**Made with ❤️ by Nick — Ngo Huu Loc**

*"Choose to become extraordinary"*

</div>
