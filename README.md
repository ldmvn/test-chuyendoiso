# Chợ Nhà

Monorepo JavaScript cho cửa hàng trực tuyến: HTML/CSS/JS mobile-first ở `apps/storefront`, Cloudflare Worker REST API ở `apps/api`, và Cloudflare D1 để lưu sản phẩm, khách hàng, đơn hàng.

## Chạy local

Yêu cầu Node.js 20+ và npm.

1. Cài dependencies: `npm.cmd install`
2. Tạo database D1 local và nạp dữ liệu mẫu: `npm.cmd run db:local`
3. Tạo mã quản trị dùng local: `npx.cmd wrangler secret put ADMIN_TOKEN --local`
4. Chạy ứng dụng: `npm.cmd run dev`
5. Mở URL Wrangler in ra; trang quản trị nằm ở `/admin.html`.

 Wrangler tạo database local từ migration; không cần tạo D1 từ xa để phát triển. Giá trị `database_id` mẫu trong `wrangler.jsonc` chỉ cần thay bằng ID thật khi triển khai lên Cloudflare.

## Triển khai Cloudflare

1. Tạo D1 database `cho-nha-db` trong Cloudflare và thay `database_id` trong `wrangler.jsonc` bằng ID được cấp.
2. Chạy `npm.cmd run db:remote` để áp dụng migration và dữ liệu khởi tạo.
3. Chạy `npx.cmd wrangler secret put ADMIN_TOKEN` để đặt token quản trị production.
4. Chạy `npm.cmd run deploy`.

## Chức năng

- Khách hàng: xem sản phẩm, lọc danh mục, tìm kiếm, giỏ hàng lưu trên trình duyệt và tạo đơn hàng.
- Trợ lý chat dùng Cloudflare Workers AI khi binding `AI` khả dụng, tra cứu sản phẩm còn hàng và có phản hồi dự phòng theo câu hỏi thường gặp.
- Quản trị: xem tổng quan, quản lý sản phẩm/danh mục, xem khách hàng và cập nhật trạng thái đơn hàng.
- API quản trị yêu cầu `ADMIN_TOKEN` qua `Authorization: Bearer ...`; token trên giao diện chỉ lưu trong session của tab.
- Thanh toán hiện ghi nhận lựa chọn thanh toán khi nhận hàng hoặc chuyển khoản thủ công. Đơn hàng được tạo ở trạng thái chờ xác nhận; chưa có cổng thanh toán trực tuyến.

Workers AI cần được bật cho tài khoản Cloudflare trước khi triển khai. Nếu binding AI chưa dùng được, chatbot vẫn trả lời các câu hỏi cơ bản và tìm sản phẩm.

## Cấu trúc

```text
apps/
  api/src/index.js       Cloudflare Worker API
  storefront/            HTML, CSS và vanilla JavaScript
migrations/              D1 schema và dữ liệu mẫu
wrangler.jsonc           Worker, static assets và D1 binding
```