# MINI-PROJECT SHORT TECHNICAL REPORT
**Course:** Cross-Platform Mobile App Development (Cross-Platform Mobile & Web Development — VKU)  
**Mini-Project Title:** Mini-Project 1: VKU Field Survey — Hệ thống Khảo sát & Kiểm tra Cơ sở Vật chất Khuôn viên VKU (Offline-First PWA & Capacitor Mobile Bridge)  
**Student Name:** Vi Trung Việt  
**Submission Date:** 19/10/2026  

---

## 1. GENERAL INFORMATION & DELIVERABLE LINKS
* **Team Members / Student:**
  1. **Vi Trung Việt** — Student ID: `23IT.B245` (Class: `23ITB`, Email: `vietvt.23itb@vku.udn.vn`) — Role: *Lead Architect / Full-Stack Cross-Platform Mobile & UI Engineering* — Contribution: **100%**
* **🔗 Live Demo URL:** [https://vku-field-survey.vietvt-23itb.workers.dev/](https://vku-field-survey.vietvt-23itb.workers.dev/)
* **💻 GitHub Repository:** [https://github.com/Penguincodea/vku-field-survey](https://github.com/Penguincodea/vku-field-survey)
* **📱 Android APK Download:** [GitHub Releases Latest APK](https://github.com/Penguincodea/vku-field-survey/releases)
* **🎥 Video Demo (Optional):** *Có thể đính kèm video kiểm thử thực tế trên thiết bị vật lý*

---

## 2. FEATURE IMPLEMENTATION CHECKLIST

| # | Required Feature | Status | Implementation Details & Acceptance Level |
|:---:|---|:---:|---|
| **1** | **PWA Standalone & W3C Installability** | ✅ Complete | Cấu hình chuẩn `manifest.json`, `display: standalone`, theme-color `#0284c7`, bộ icon 192x192 & 512x512; cài đặt mượt mà dạng ứng dụng độc lập trên Android, iOS và Desktop Chrome/Edge. |
| **2** | **Cache-First App Shell Service Worker** | ✅ Complete | Tệp `sw.js` cache toàn bộ App Shell (HTML, CSS, JS bundles, Web Fonts, SVGs); ứng dụng khởi động tức thì < 150ms ngay cả khi thiết bị mất kết nối mạng hoàn toàn. |
| **3** | **Interactive 3-Step Field Inspection Form** | ✅ Complete | Quy trình kiểm định chuẩn hóa 3 bước: (1) 10 Phân khu VKU & Vị trí phòng học, (2) Danh mục trang thiết bị & Tag khiếm khuyết chọn nhanh, (3) Hiện trạng, mức độ ưu tiên & Đánh giá 1-5 sao. |
| **4** | **Real-time Draft Recovery (IndexedDB)** | ✅ Complete | Tích hợp thư viện `idb` lưu snapshot bản nháp tức thì vào Object Store `drafts` sau mỗi tương tác input. Phục hồi 100% dữ liệu khi vô tình vuốt tắt app hoặc tải lại trang. |
| **5** | **Offline Sync Queue (Sequential Flushing)** | ✅ Complete | Lưu phiếu vào `pending_submissions` khi mất mạng với cờ `PENDING_SYNC`. Khi có Internet, Sync Engine kích hoạt duyệt tuần tự (`for...of` + `await`) để đẩy lên backend, ngăn chặn nghẽn server và xung đột dữ liệu. |
| **6** | **Camera & Client-Side Canvas Compression** | ✅ Complete | Sử dụng HTML5 Canvas ngầm nén ảnh chụp hiện trường độ phân giải cao về chuẩn JPEG chất lượng 0.72 với kích thước khống chế < 250KB, lưu trực tiếp dưới dạng Blob/Base64 an toàn. |
| **7** | **Hardware GPS Coordinates Tagging** | ✅ Complete | Tích hợp Geolocation API / `@capacitor/geolocation` tự động định vị tọa độ thực tế tại khuôn viên trường VKU (15.9740°N, 108.2518°E) gắn kèm mỗi bản ghi kiểm tra. |
| **8** | **Inspection Audit Dashboard & Reporting** | ✅ Complete | Bảng điều khiển thời gian thực với Ribbon 4 chỉ số KPI, tìm kiếm đa tiêu chí, lọc theo phân khu/trạng thái, Lightbox xem ảnh, xuất dữ liệu ra CSV (UTF-8) và in biên bản kiểm toán PDF. |
| **9** | **Native Android APK via Capacitor Bridge** | ✅ Complete | Tích hợp `@capacitor/core`, `@capacitor/camera`, `@capacitor/network`, `@capacitor/android`; đóng gói và biên dịch thành công file APK native chạy độc lập trên điện thoại Android. |
| **10** | **Cyber-Glass Inspector Cockpit UI** | ✅ Complete | Giao diện chuẩn Dark Mode Obsidian Glass cao cấp với bảng màu phát quang Neon Cyan/Emerald VKU, Typography hình học Plus Jakarta Sans & JetBrains Mono, tương phản chuẩn WCAG AA. |

---

## 3. TECHNICAL ARCHITECTURE & PROJECT STRUCTURE

### 3.1. Directory Structure
```text
vku-field-survey/
├── android/                   # Dự án Native Android đóng gói qua Capacitor
│   └── app/src/main/          # AndroidManifest, Native Java Activity & Assets
├── functions/api/             # Cloudflare Pages Serverless Function (/api/submissions)
├── public/                    # Tài nguyên tĩnh PWA (manifest.json, sw.js, icons)
├── src/
│   ├── assets/                # Logo, vector icons đồ họa
│   ├── db/                    # Tầng truy xuất cơ sở dữ liệu IndexedDB (idb wrapper)
│   ├── form/                  # Logic nghiệp vụ Form & Bảng điều khiển
│   │   ├── camera.ts          # Nén ảnh Canvas client-side & xử lý Blob
│   │   ├── steps.ts           # Quản lý State 3 bước, Render form & Sidebar HUD
│   │   └── history.ts         # Quản lý Lịch sử, KPI Metrics, Lọc, Xuất CSV/PDF
│   ├── sw/                    # Cấu hình Service Worker Cache-First
│   ├── sync/                  # Sequential Offline Background Sync Engine
│   ├── utils/                 # Module tiện ích GPS (geo.ts) & Toast Alert (toast.ts)
│   ├── main.ts                # App entrypoint, điều hướng SPA, Network Listener
│   ├── style.css              # Cyber-Glass UI Design System (Obsidian & Neon Cyan)
│   └── worker.ts              # Entrypoint Cloudflare Worker API & Static Router
├── capacitor.config.ts        # Cấu hình Capacitor Native Bridge
├── index.html                 # App Shell HTML & Splash Screen
├── vite.config.ts             # Cấu hình Vite Build Tooling
└── wrangler.json              # Cấu hình Serverless Cloudflare Deploy
```

### 3.2. Data Flow & State Management Architecture
```text
  [Người kiểm tra thao tác Form 3 bước] 
                   │ (Tự động chụp snapshot sau mỗi input)
                   ▼
       [IndexedDB: Store 'drafts'] ───(Khôi phục 100% khi reload / mở lại app)
                   │ 
          [Bấm: Lưu & Gửi Báo Cáo]
                   ▼
  ┌──────────────────────────────────────────────────────────┐
  │ • Nén ảnh Canvas: 10MB -> ~200KB JPEG (Blob)             │
  │ • Lấy tọa độ GPS: navigator.geolocation / Capacitor Geo   │
  │ • Gán UUID v4 + Timestamp + Trạng thái: PENDING_SYNC     │
  └────────────────────────┬─────────────────────────────────┘
                           ▼
          [Lưu vào IndexedDB: Store 'submissions']
                           │
                 [Sync Engine Kiểm tra Mạng]
              ┌────────────┴────────────┐
              │                         │
     [Thiết bị MẤT MẠNG]        [Thiết bị CÓ MẠNG]
              │                         │
     • Giữ hàng đợi offline    • Duyệt tuần tự (for...of + await)
     • Hiển thị badge Chờ      • POST /api/submissions
     • Lắng nghe online event  • HTTP 200 OK -> Chuyển status: SYNCED
              │                         ▲
              └────────(Khi có mạng)────┘
```

---

## 4. EMPIRICAL EVIDENCE & SCREENSHOTS

*Ứng dụng đã được kiểm thử thực tế trên cả môi trường Desktop Web, Mobile Browser và Ứng dụng Android APK:*

1. **Màn hình Form Bước 1 — Phân khu VKU & Định vị GPS:**
   - 10 Phân khu cơ sở vật chất (Khu V, Khu K, Khu B, Khu A...) hiển thị trực quan dưới dạng thẻ điều khiển buồng lái công nghệ cao. Dải GPS tự động hiển thị tọa độ thực tế kèm chip chọn nhanh tầng và số phòng chuẩn thời khóa biểu VKU.
2. **Màn hình Form Bước 2 — Thiết bị & Tag Khiếm khuyết nhanh:**
   - Phân loại trang thiết bị (Máy tính Lab, Máy chiếu, Điều hòa, Hệ thống điện, Bàn ghế) kèm danh mục tag lỗi chọn nhanh trong 1 chạm có gắn đèn LED trạng thái đổi màu.
3. **Màn hình Form Bước 3 — Đánh giá Sao & Camera Nén Ảnh:**
   - Hệ thống đánh giá 1-5 sao phát quang rực rỡ, bộ 3 nút chọn mức độ ưu tiên (*Bình thường, Cần xử lý, Khẩn cấp*) và khung ngắm camera hiện trường tích hợp nén ảnh Canvas tự động.
4. **Bảng điều khiển Lịch sử Kiểm tra (Audit Dashboard):**
   - Ribbon 4 chỉ số KPI thời gian thực (*Tổng số, Chờ đồng bộ, Đã đồng bộ, Điểm trung bình*), thanh lọc đa năng, danh sách thẻ phiếu kiểm tra dạng Glassmorphism, xem chi tiết Modal, Lightbox ảnh và nút xuất CSV UTF-8.

---

## 5. TECHNICAL CHALLENGES & RESOLUTIONS

### Thách thức 1: Lỗi quá tải dung lượng (Asset Too Large > 25 MiB) khi Deploy Cloudflare
* **Vấn đề:** Cloudflare Workers & Pages giới hạn dung lượng tối đa cho mỗi tệp tĩnh là 25 MiB. Ban đầu, tệp `vku-field-survey.apk` (42 MiB) được đặt trong thư mục `public/`, dẫn đến việc Vite tự động copy vào `dist/` khi build, làm Cloudflare từ chối lệnh deploy (`✘ [ERROR] Asset too large`). Ngoài ra, file APK này còn bị Capacitor copy ngược vào bundle Android, làm tăng gấp đôi dung lượng ứng dụng native.
* **Giải pháp:** 
  - Loại bỏ hoàn toàn tệp APK khỏi `public/` và `dist/`, bổ sung quy tắc `*.apk` vào `.gitignore` để giữ kho mã nguồn tinh gọn (~400 KB).
  - Tách luồng phân phối file APK sang **GitHub Releases** ([Releases Link](https://github.com/Penguincodea/vku-field-survey/releases)) và trỏ nút *"Tải APK"* trong Header trực tiếp về GitHub Releases, giúp tải không giới hạn băng thông và dung lượng.

### Thách thức 2: Đồng bộ hóa Giao diện giữa Web PWA và File APK Android
* **Vấn đề:** Bản đóng gói Android APK ban đầu sở hữu giao diện Dark Blue Glassmorphism sang trọng, nhưng mã nguồn Web bị ghi đè bởi hơn 1.200 dòng CSS thừa ở cuối file (`/* ── Refined visual system ── */`), ép giao diện Web sang màu nền xám xanh nhạt gây lệch hoàn toàn so với bản APK.
* **Giải pháp:**
  - Dọn dẹp triệt để các khối CSS xung đột, tái cấu trúc toàn diện hệ thống style theo chuẩn **Cyber-Glass Inspector Cockpit** đồng nhất cho cả nền tảng Web và Native Android.
  - Tích hợp typography hiện đại `Plus Jakarta Sans` và `JetBrains Mono`, chuẩn hóa màu sắc VKU Neon Cyan (`#00f0ff`) và Sapphire Blue (`#0284c7`), mang lại trải nghiệm thị giác ấn tượng và nhất quán 100% trên mọi thiết bị.
