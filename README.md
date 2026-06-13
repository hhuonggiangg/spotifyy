# 🎵 Spotify Popularity Predictor — Vercel Edition

Phiên bản **static frontend + serverless function**, viết lại từ app Streamlit để
chạy **native trên Vercel** (Streamlit không chạy được trên Vercel — đây là lý do
bản này tồn tại).

- `index.html` — toàn bộ UI + hiệu ứng (floating notes, aurora, gauge & radar
  tương tác bằng Plotly, confetti, toast). Vercel phục vụ qua CDN.
- `data.js` — dữ liệu nhạc (249 bài, 83 nghệ sĩ) sinh sẵn.
- `api/predict.py` — **serverless function** Python tính điểm popularity
  (`POST /api/predict`). Chỉ dùng thư viện chuẩn nên deploy nhẹ, không lỗi size.
- `vercel.json` — cấu hình (framework = Other, không build step).

> Frontend gọi `/api/predict`; nếu function lỗi/khởi động chậm thì **tự fallback**
> sang công thức tính ngay trong trình duyệt, nên app luôn chạy.

---

## 🚀 Deploy lên Vercel

### Cách 1 — Qua GitHub (khuyến nghị)
1. Đưa **4 file** (`index.html`, `data.js`, `vercel.json`, thư mục `api/`) vào
   **thư mục gốc** repo `hhuonggiangg/predictor` (thay cho code Streamlit cũ).
   - Xoá các file Streamlit cũ: `app.py`, `requirements.txt`, `Procfile`,
     `Dockerfile`, `render.yaml`, `api/index.py` cũ… để khỏi lẫn.
2. Trên Vercel, mở project `predictor` → **Settings → General**:
   - **Framework Preset:** Other
   - **Build Command:** để trống
   - **Output Directory:** `.` (dấu chấm)
   - **Root Directory:** để trống (gốc repo)
3. **Save** rồi vào tab **Deployments → Redeploy** (hoặc push commit mới).

Xong — `https://predictor-zeta-kohl.vercel.app` sẽ hiện app thật thay vì 404.

### Cách 2 — Vercel CLI
```bash
npm i -g vercel
cd thư-mục-chứa-4-file
vercel        # deploy preview
vercel --prod # deploy production
```

---

## 🧪 Chạy thử local
```bash
# Mở tĩnh (prediction chạy bằng fallback trong JS):
python3 -m http.server 8000   # rồi mở http://localhost:8000

# Hoặc chạy luôn cả serverless function:
npm i -g vercel && vercel dev
```
Đăng nhập demo: **demo / demo123**

---

## ❓ Vì sao phải viết lại, không "sửa" Streamlit?
Vercel chạy code Python dưới dạng **serverless function ngắn hạn**, không nuôi nổi
server WebSocket sống liên tục mà Streamlit cần → deploy báo "Ready" nhưng truy cập
ra **404 / NOT_FOUND** (đúng như màn hình bạn gặp). Static HTML + một function
`/api/predict` là kiến trúc Vercel hỗ trợ, nên bản này lên được và chạy mượt.

> Nếu sau này muốn dùng đúng app Streamlit gốc, hãy deploy ở **Streamlit Community
> Cloud / Render / Hugging Face Spaces** — ở đó nó chạy nguyên bản không cần sửa.
