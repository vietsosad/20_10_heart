# 💕 Heart — trang kỉ niệm bằng ảnh

Một trang web tĩnh, một file duy nhất: 20 tấm ảnh xếp thành hình trái tim, mỗi lần click mở ra một tấm. Mở hết ảnh rồi click thêm một lần nữa thì cả màn hình biến thành một đàn bướm hồng bay tràn ra.

Không framework, không build, không dependency — chỉ HTML + CSS + Canvas 2D thuần.

---

## Chạy thử

```bash
# cách 1: mở thẳng file
start heart.html          # Windows
open heart.html           # macOS

# cách 2: chạy qua server tĩnh (nếu trình duyệt chặn file://)
python -m http.server 8000
# rồi mở http://localhost:8000/heart.html
```

> **Repo này không chứa ảnh.** Toàn bộ ảnh cá nhân đã được loại khỏi git (xem [`.gitignore`](.gitignore)). Clone về sẽ thấy 20 ô ảnh trống — hãy tự thêm ảnh của bạn theo hướng dẫn bên dưới.

## Dùng ảnh của bạn

1. Bỏ ảnh vào cùng thư mục với `heart.html`.
2. Sửa mảng `photos` ở đầu thẻ `<script>` trong `heart.html`:

```js
// ========== PHOTO LIST ==========
const photos = [
    'anh-01.jpg',
    'anh-02.jpg',
    // ... bao nhiêu tấm cũng được
];
```

Số lượng ảnh là tuỳ ý — trái tim tự tính lại vị trí cho đúng số ảnh bạn đưa vào. Ảnh nên vuông hoặc gần vuông (ảnh sẽ bị `object-fit: cover` cắt về ô 90×90).

3. Đổi tên và câu chữ trong khối `#title-text` của phần `<body>`.

---

## Nhạc nền

Trang hỗ trợ **cả hai cách**: nhúng YouTube, hoặc dùng file nhạc để cạnh `heart.html`. Bản này đang nhúng sẵn *The Walters — I Love You So* từ YouTube. Để trống cả hai trường thì trang chạy im lặng và đĩa nhạc không hiện.

Sửa khối `MUSIC` trong `heart.html` (khối `NHẠC NỀN`):

```js
const MUSIC = {
    youtubeId: 'NwFVSclD_uc',  // phần sau ?v= trong link YouTube
    file: '',                  // hoặc 'nhac.mp3' — để cạnh heart.html
    volume: 0.35,              // 0 – 1
    startAt: 0,                // giây, chỉ áp dụng cho YouTube
    fadeIn: 2500,              // ms, nhạc lớn dần lên cho đỡ giật mình
};
```

Chỉ cần phần id sau `?v=` trong link. Nếu link của bạn có thêm `&list=RD...` thì bỏ qua phần đó — đó là danh sách "radio" YouTube tự sinh cho từng người, không nhúng ổn định; ở đây một bài được cho lặp lại liên tục.

Nhạc **bắt đầu ở click đầu tiên**, cùng lúc với tấm ảnh đầu tiên. Đây không phải lựa chọn thẩm mỹ mà là bắt buộc: trình duyệt không cho phát tiếng trước khi người dùng thao tác. Trang này click-to-play sẵn nên vừa vặn.

Giao diện nhạc là **một cái đĩa than ở góc dưới bên trái**: quay khi đang phát, đứng yên và xỉn lại khi tắt tiếng. Bấm vào đĩa để tắt/bật. Đĩa vẽ hoàn toàn bằng CSS (rãnh đĩa là `repeating-radial-gradient`), không dùng ảnh bìa album.

### Chọn cách nào?

|  | YouTube | File nhạc |
|---|---|---|
| Chạy bằng `file://` (mở thẳng file) | ❌ player không hiện | ✅ chạy |
| Chạy offline | ❌ | ✅ |
| Đẩy lên repo công khai | ✅ nhạc nằm ở server YouTube | ⚠️ đừng commit nhạc có bản quyền |
| Có quảng cáo chèn trước | có thể | không |
| Rủi ro tắt tiếng | video bị gỡ / chặn nhúng / khoá vùng | không |

**Nếu dùng YouTube, cần biết ba điều:**

1. **Phải chạy qua http(s)**, không mở thẳng bằng `file://` — đã thử: qua `file://` iframe được tạo nhưng player không hiện. Dùng `python -m http.server` hoặc GitHub Pages.
   Và phải vào bằng **`http://localhost:8000`**, đừng vào bằng `http://127.0.0.1:8000`: cùng một server, cùng một trang, nhưng YouTube trả lỗi 150 (chặn nhúng) cho origin `127.0.0.1` trong khi `localhost` thì chạy bình thường. Đã thử cả hai để chắc.
2. **Video phải cho phép nhúng.** Nhiều video (đặc biệt bản ghi livestream, MV hãng đĩa) trả lỗi *"Video unavailable"* khi nhúng. Khi đó `onError` sẽ tự chuyển sang `MUSIC.file` nếu bạn có điền, còn không thì đĩa nhạc tự ẩn đi.
3. **Khung player YouTube bị đẩy ra ngoài khung nhìn** (`#yt-host`, `left: -320px`) vì giao diện nhạc là cái đĩa than. Cần biết: điều khoản nhúng của YouTube yêu cầu player phải nhìn thấy được, nên cách này là trái điều khoản. Iframe vẫn giữ nguyên kích thước thật 200×113 thay vì thu về 1px — đã đối chứng hai cách đặt (ngoài khung nhìn và nằm dưới đĩa), trình duyệt xử lý y hệt nhau.

**Nếu dùng file nhạc:** `.gitignore` đã chặn sẵn `*.mp3 *.m4a *.wav *.ogg *.flac`. Nhạc có bản quyền mà commit lên repo công khai là chuyện vi phạm bản quyền, nên file nhạc chỉ nằm trong máy bạn — ai clone repo về sẽ phải tự chuẩn bị nhạc, giống như với ảnh.

Điền cả hai nguồn cũng được: YouTube chạy trước, hỏng thì tự rơi về file.

---

## Luồng trải nghiệm

| Bước | Diễn ra |
|---|---|
| Vào trang | Nền tối, tim nhỏ trôi lơ lửng, dòng gợi ý "click để mở từng khoảnh khắc" |
| Click lần 1 | Gợi ý biến mất, tên hiện ra, tấm ảnh đầu bung lên kèm chùm tim bắn ra, nhạc bắt đầu lớn dần |
| Click tiếp | Mở lần lượt từng ảnh, bộ đếm góc phải chạy `n / 20` |
| Mở hết ảnh, click thêm | **Màn cuối:** bướm xuất hiện theo 7 đợt, từ 3 con lên hơn 300 con |
| ~10 giây sau | Màn hồng phủ dần toàn màn hình |
| ~14 giây sau | Click bất kỳ để reset về đầu |

---

## Cấu trúc code

Tất cả nằm trong `heart.html`, chia theo các khối comment:

| Khối | Việc nó làm |
|---|---|
| `PHOTO LIST` | Danh sách ảnh |
| `HEART SHAPE POSITIONS` | Phương trình tim `x = 16sin³t`, `y = -(13cos t − 5cos 2t − 2cos 3t − cos 4t)`, lấy mẫu đều theo số ảnh rồi map vào khung chứa |
| `REVEAL LOGIC` | Đếm click, mở ảnh, bộ đếm, ẩn/hiện tiêu đề |
| `NHẠC NỀN` | Cấu hình `MUSIC`, nạp YouTube IFrame API hoặc `<audio>`, fade-in, đĩa than vừa làm hình vừa làm nút tắt/bật, dự phòng khi video lỗi |
| `PARTICLE SYSTEM` | Tim trôi nền (25 hạt, tự tái sinh) + chùm tim bắn ra mỗi lần mở ảnh |
| `BUTTERFLY HEART FINALE` | Toàn bộ màn cuối — xem phần dưới |
| `MAIN ANIMATION LOOP` | Một `requestAnimationFrame` duy nhất chạy cả hai canvas |

Ba lớp vẽ chồng lên nhau: `#particles-canvas` (z 0) → ảnh (z 1) → `#butterfly-canvas` (z 100) → `#pink-flood` (z 200).

### Màn bướm hoạt động thế nào

Mỗi con bướm được vẽ bằng **một lệnh `drawImage`** mỗi khung hình, nhờ dựng sprite trước:

- Lúc bắt đầu màn cuối, `renderButterflySprites()` vẽ sẵn 6 bảng màu × 16 khung nhịp cánh vào canvas ngoài màn hình (dải 2048×128 mỗi bảng màu). Mỗi khung là một độ mở cánh khác nhau trong một nhịp đập.
- Chi tiết cánh — gradient, gân, dải viền tối, chấm ngọc dọc mép, ánh loé trên mép trước, quầng sáng blur — đều được **nướng sẵn vào sprite**, nên hơn 300 con bướm cùng lúc vẫn nhẹ hơn cách vẽ lại bezier từng khung.
- Hình học cánh viết trong hệ quy chiếu "mũi hướng +x": `traceForewing()` (cánh trước dài, vuốt ra trước, mép ngoài hơi lõm) và `traceHindwing()` (cánh sau tròn, viền lượn sóng, có thùy đuôi). Vẽ nửa trên rồi lật `scale(1, -1)` cho nửa dưới.

Phần bay:

- Bướm **lượn bằng cách đổi hướng mượt** (`heading` cộng dồn hai sóng sin lệch tần số), không phải rung vận tốc — đó là khác biệt giữa "con bướm" và "con bọ trên lò xo".
- Vọt lên ở nhịp đập cánh xuống, trôi ở nhịp lên; thân nhún theo trục dọc của chính nó mỗi nhịp.
- Bướm nhỏ đập cánh nhanh hơn, và được vẽ trước — tạo cảm giác xa gần mà không cần z-buffer.
- Khoảng 45% số bướm kéo theo vệt tim lấp lánh mờ dần.
- Ra khỏi mép màn hình thì vòng lại phía đối diện, nên đàn bướm trông như vô tận.

### Vài chỗ dễ chỉnh

| Muốn đổi | Sửa ở đâu |
|---|---|
| Số bướm và nhịp các đợt | mảng `waves` trong `startButterflyPhase()` |
| Màu bướm | `butterflyColors` — mỗi bảng có `in` (trong cánh), `out` (ngoài cánh), `rim` (viền/gân), `body`, `glow`, `spot` |
| Kích thước bướm | `const size = 12 + Math.random() * 20` trong `createButterfly()` |
| Độ mượt nhịp cánh | `FLAP_FRAMES` (mặc định 16) |
| Độ nét sprite | `FRAME` (mặc định 128px mỗi khung) |
| Thời điểm phủ hồng / cho reset | mốc `elapsed > 10` và `elapsed > 14` trong `updateButterflies()` |
| Số tim trôi nền | `AMBIENT_COUNT` |
| Kích thước / vị trí đĩa nhạc | `#record` trong CSS (66px, góc dưới trái) |
| Tốc độ đĩa quay | `recordSpin` — mặc định 4.5s một vòng |
| Nhạc nền | khối `MUSIC` — xem phần [Nhạc nền](#nhạc-nền) |

---

## Tương thích

Chrome, Edge, Firefox, Safari bản mới. Dùng `ctx.filter = 'blur()'` khi dựng sprite quầng sáng — trình duyệt quá cũ không hỗ trợ thì bướm vẫn hiện, chỉ mất quầng sáng. Font lấy từ Google Fonts (Great Vibes + Cormorant Garamond), offline thì rơi về font serif hệ thống.

Chạy mượt trên desktop; trên máy yếu, giảm đợt cuối trong `waves` xuống.
