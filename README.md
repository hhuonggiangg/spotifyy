# 🎵 Spotify Popularity Predictor

Một web app single-page (HTML/CSS/JS thuần, **không framework**) dự đoán **độ phổ biến (0–100)** của một bài hát dựa trên các *audio features* (danceability, energy, valence, …). Ngoài dự đoán, app còn cho phép duyệt bảng xếp hạng, nghe preview 20 giây, xem analytics và lưu bài hát yêu thích.

---

## ✨ Tính năng chính

| View | Mô tả |
|------|-------|
| **Predictor** | Nhập tên bài + nghệ sĩ, chỉnh 7 slider audio features (hoặc chọn preset), bấm *Predict* để nhận điểm popularity kèm gauge, radar "Audio DNA", insights và nút chia sẻ. |
| **Browse** | Bảng xếp hạng bài hát có lọc theo popularity, tìm kiếm, sắp xếp (popularity/dance/energy/valence/tempo), "Surprise me" ngẫu nhiên, load thêm dần. |
| **Analytics** | Dashboard thống kê: trung bình các feature, Top 10 nghệ sĩ, radar "Audio DNA" trung bình, histogram phân bố popularity, leaderboard nghệ sĩ mở rộng được. |
| **Favorites** | Danh sách bài đã lưu, phát preview, xoá, và **export ra CSV**. |

### Tính năng phụ
- 🎧 **Audio preview 20s** — lấy preview + ảnh bìa/nghệ sĩ thật từ **Deezer API** (fallback **iTunes**), gọi qua JSONP nên chạy được trên file tĩnh, không cần backend.
- 📊 **Đồ hoạ SVG tự vẽ** — gauge, radar chart, histogram (không dùng thư viện chart).
- 🖼️ **Lazy-load ảnh nghệ sĩ** qua `IntersectionObserver`.
- 🎉 Confetti, toast notifications, equalizer animation, skeleton shimmer.
- ♿ A11y: `aria-label`, `focus-visible`, `prefers-reduced-motion`.
- 📱 Responsive: sidebar desktop → bottom tab bar trên mobile.
- 💾 Lưu trạng thái (favorites, số lần dự đoán) trong `localStorage`.

---

## 🚀 Chạy app

App là file tĩnh, chỉ cần một HTTP server bất kỳ (mở trực tiếp `file://` sẽ chặn JSONP/fetch):

```bash
# Python
python3 -m http.server 8000

# hoặc Node
npx serve .
```

Mở `http://localhost:8000`.

**Đăng nhập demo:** username `demo` / password `demo123` (thực ra nhập username bất kỳ là vào được — auth chỉ là mô phỏng phía client).

---

## 📁 Cấu trúc

```
index.html      # toàn bộ UI + logic (CSS + JS inline)
data.js         # ⚠️ BẮT BUỘC — định nghĩa window.SONGS và window.ARTISTS
```

> **Lưu ý:** `index.html` nạp `data.js` qua `<script src="data.js"></script>`. File này **không có trong bản upload** — nếu thiếu, `SONGS`/`ARTISTS` sẽ rỗng và các view Browse/Analytics sẽ trống. Cần tạo `data.js` theo schema dưới đây.

### Schema dữ liệu

```js
// data.js
window.SONGS = [
  {
    track: "Blinding Lights",
    artist: "The Weeknd",
    pop: 95,          // popularity 0–100
    dance: 0.51, energy: 0.73, valence: 0.33,
    acoustic: 0.00, speech: 0.06, live: 0.09,
    tempo: 171
  },
  // ...
];

window.ARTISTS = {
  "the weeknd": {                    // key = tên nghệ sĩ viết thường
    bio: "Canadian singer...",
    genres: ["Pop", "R&B"],
    hits: ["Blinding Lights", "Starboy"]
  },
  // ...
};
```

---

## 🧠 Cơ chế dự đoán

Hàm `predict()` thử gọi backend trước, nếu thất bại thì fallback về công thức cục bộ:

```js
// Gọi POST /api/predict, nếu lỗi → localScore()
function localScore(f) {
  return clamp(f.dance*30 + f.energy*25 + f.valence*20 + 25, 0, 100);
}
```

Hiện tại chưa có backend `/api/predict`, nên app luôn dùng công thức tuyến tính cục bộ. (Xem phần *Đề xuất* để thay bằng model thật.)

---

## 🔧 Stack kỹ thuật

- **Vanilla JS** — không build step, không dependency runtime.
- **canvas-confetti** (CDN) — hiệu ứng confetti.
- **Deezer + iTunes Search API** (JSONP) — ảnh & preview audio.
- **Plus Jakarta Sans + Anton** (Google Fonts).
- SVG vẽ tay cho mọi biểu đồ.

---

## ⚠️ Hạn chế hiện tại

- "Đăng nhập" chỉ là mô phỏng — không có xác thực thật.
- Dự đoán dùng công thức tuyến tính cố định, không phải ML thật.
- Phụ thuộc Deezer/iTNES còn sống và CORS/JSONP cho phép.
- Không có test, không có backend.
