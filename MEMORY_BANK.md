# MEMORY BANK - VeritaShop Ecommerce Mobile App

## 📋 TỔNG QUAN DỰ ÁN

**VeritaShop** là ứng dụng thương mại điện tử chuyên bán điện thoại di động, được xây dựng với kiến trúc:
- **Frontend**: Flutter 3.8.1+ (Mobile: iOS, Android, Web, Desktop)
- **Backend**: Node.js 18+ + Express.js
- **Database**: MongoDB Atlas
- **Cloud Storage**: Cloudinary (upload ảnh)
- **Payment**: MoMo (sandbox), COD
- **Target Market**: Việt Nam (giao diện tiếng Việt)

---

## 🏗️ KIẾN TRÚC DỰ ÁN

### Frontend Structure (Flutter)
```
lib/
├── core/                   # Core functionalities
│   ├── constants/         # App constants
│   ├── network/           # API service (Dio), interceptors
│   ├── routes/            # App routing
│   ├── services/          # Local notifications, MoMo, PIN, Voice Search
│   ├── theme/             # Dark/Light themes
│   └── utils/             # Currency formatter
├── data/
│   ├── models/            # Data models (Product, Order, Cart, Review, etc.)
│   └── repositories/      # Data layer (API calls)
└── presentation/
    ├── screens/           # UI screens (Customer & Admin)
    ├── view_models/       # State management (Provider)
    └── widgets/           # Reusable widgets
```

### Backend Structure (Node.js)
```
backend/src/
├── config/               # Database, middleware config
├── controllers/          # Route handlers
├── middleware/           # Auth, admin, content filter
├── models/               # Mongoose schemas
├── routes/               # API routes
├── services/             # MoMo payment service, file export
├── utils/                # Helpers, seed, sentiment analysis
└── app.js                # Express app setup
```

---

## 🎯 CHỨC NĂNG CHÍNH - CUSTOMER APP

### 1. 🔐 XÁC THỰC
**Screens:**
- `login_screen.dart` - Đăng nhập với email/password
- `register_screen.dart` - Đăng ký tài khoản mới
- `forgot_password_screen.dart` - Quên mật khẩu qua email
- `pin_setup_screen.dart` - Thiết lập PIN bảo mật
- `pin_lock_screen.dart` - Mở khóa bằng PIN
- `change_password_screen.dart` - Đổi mật khẩu

**Tính năng:**
- JWT Authentication
- Remember me (SharedPreferences)
- PIN Lock bảo mật thêm lớp
- Quên mật khẩu gửi link reset qua email (Nodemailer)
- Session persistence (FlutterSecureStorage)

---

### 2. 📱 SẢN PHẨM
**Screens:**
- `product_list_screen.dart` - Danh sách sản phẩm
- `product_detail_screen.dart` - Chi tiết sản phẩm

**Tính năng:**
- Hiển thị danh sách sản phẩm với phân trang
- Server-side search (MongoDB text search)
- Lọc theo: Brand (iPhone, Samsung, Xiaomi, OPPO, Vivo), Giá, Tình trạng (Mới, Like New, Đã dùng)
- Sắp xếp: Giá, Rating, Mới nhất
- Chi tiết sản phẩm: Thông số kỹ thuật (RAM, ROM, Chip, Pin, Màn hình, Camera), Hình ảnh, Đánh giá
- Màu sắc sản phẩm
- Thêm vào giỏ hàng / Wishlist
- Đánh giá sản phẩm 1-5 sao với hình ảnh (tối đa 5 ảnh)
- Phân tích cảm xúc tự động (ABSA - Aspect-Based Sentiment Analysis)

**Product Model:**
```dart
ProductModel {
  id, name, brand, description,
  price, originalPrice, discountPercent,
  images, category,
  specs: {ram, rom, chip, battery, screen, camera},
  colors: [{name, code, image}],
  condition: 'new' | 'likenew' | 'used',
  warranty, rating, reviewCount, stock, isFeatured, tags
}
```

---

### 3. 🛒 GIỎ HÀNG & WISHLIST
**Screens:**
- `cart_screen.dart` - Quản lý giỏ hàng
- `wishlist_screen.dart` - Danh sách sản phẩm yêu thích

**Tính năng:**
- Thêm/Xóa sản phẩm khỏi giỏ hàng
- Cập nhật số lượng
- Chọn màu sắc
- Tính tổng tiền (subtotal, shipping fee, tax)
- Miễn phí ship khi đơn hàng >= 500,000 VND
- Thêm sản phẩm yêu thích vào wishlist

**Cart Model:**
```dart
CartModel {
  id, userId, productId, productName, productBrand,
  productImageUrl, price, originalPrice,
  quantity, color: {name, code},
  stock, createdAt, updatedAt
}

CartSummary {
  items, subtotal, shippingFee, tax, total
}
```

---

### 4. 📦 THANH TOÁN & ĐẶT HÀNG
**Screens:**
- `checkout_screen.dart` - Thanh toán
- `payment_processing_screen.dart` - Xử lý thanh toán MoMo
- `order_success_screen.dart` - Đặt hàng thành công
- `address_list_screen.dart` - Quản lý địa chỉ giao hàng

**Tính năng:**
- Chọn địa chỉ giao hàng (Thêm/Sửa/Xóa, Đặt mặc định)
- Chọn phương thức thanh toán: COD hoặc MoMo
- Áp dụng mã giảm giá (Coupon)
- Xem chi tiết đơn hàng
- Tính phí ship tự động (Miễn phí >= 500k)
- Thanh toán MoMo với deep link callback
- Local notifications cho trạng thái đơn hàng

**Checkout Flow:**
1. Review giỏ hàng → 2. Chọn địa chỉ → 3. Chọn thanh toán → 4. Xác nhận → 5. Thành công

---

### 5. 📋 LỊCH SỬ ĐƠN HÀNG
**Screens:**
- `order_history_screen.dart` - Lịch sử đơn hàng
- `order_detail_screen.dart` - Chi tiết đơn hàng

**Tính năng:**
- Xem lịch sử tất cả đơn hàng
- Xem chi tiết đơn hàng
- Hủy đơn hàng (khi trạng thái là "pending")
- Đặt lại sản phẩm đã mua
- Theo dõi trạng thái đơn hàng:
  - pending → confirmed → processing → shipping → delivered
  - cancelled

**Order Status:**
```dart
enum OrderStatus {
  pending,          // Chờ xác nhận
  confirmed,        // Đã xác nhận
  processing,       // Đang xử lý
  shipping,         // Đang giao hàng
  shipped,          // Đã giao
  delivered,        // Đã giao hàng
  completed,        // Hoàn thành
  cancelled,        // Đã hủy
  refunded          // Đã hoàn tiền
}
```

---

### 6. ⭐ ĐÁNH GIÁ SẢN PHẨM
**Screens:**
- `write_review_screen.dart` - Viết đánh giá
- `comments_screen.dart` - Xem bình luận

**Tính năng:**
- Đánh giá 1-5 sao
- Viết review với tiêu đề và nội dung
- Upload hình ảnh kèm đánh giá (tối đa 5 ảnh)
- Phân tích cảm xúc tự động (ABSA):
  - Phân tích theo từng khía cạnh: Pin, Camera, Hiệu năng, Màn hình, Thiết kế, Giá, Dịch vụ, Giao hàng
  - Cảm xúc: Tích cực / Tiêu cực / Trung tính
  - Confidence score
- Content Moderation tự động:
  - Phát hiện nội dung không phù hợp (harassment, hate, violence, sexual, self-harm, illicit)
  - Admin duyệt/từ chối
- Like đánh giá

**Review Model:**
```dart
ReviewModel {
  id, userId, userName, avatarUrl, productId,
  title, reviewText, rating, aiScore,
  sentiment: 'Tích cực' | 'Tiêu cực' | 'Trung tính',
  overallSentiment: 'positive' | 'negative' | 'neutral' | 'mixed',
  tag, images, likes, isLiked,
  sentimentAnalysis: [{
    aspect: 'Battery' | 'Camera' | 'Performance' | 'Display' | ...,
    sentiment: 'positive' | 'negative' | 'neutral' | 'none',
    confidence, scores: {positive, negative, neutral},
    aspectOnly
  }],
  isFlagged, moderationStatus: 'pending' | 'approved' | 'rejected',
  moderationResult, flaggedCategoriesVietnamese
}
```

---

### 7. 👤 TÀI KHOẢN NGƯỜI DÙNG
**Screens:**
- `profile_screen.dart` - Thông tin tài khoản
- `edit_profile_screen.dart` - Chỉnh sửa hồ sơ
- `settings_screen.dart` - Cài đặt
- `notifications_screen.dart` - Thông báo

**Tính năng:**
- Xem/Sửa thông tin cá nhân
- Quản lý địa chỉ giao hàng
- Đổi mật khẩu
- Đổi PIN bảo mật
- Dark/Light mode
- Quản lý thông báo
- Đăng xuất

---

### 8. 🔍 TÌM KIẾM VOICE SEARCH (ĐÃ XÓA)
**Service:**
- `voice_search_service.dart` - Ghi âm và chuyển đổi text thành giọng nói

**Note:** Tính năng này đã bị xóa khỏi dự án.

---

### 9. 🔔 THÔNG BÁO
**Service:**
- `local_notification_service.dart` - Local notifications

**Tính năng:**
- Thông báo đơn hàng mới
- Nhắc đánh giá sau khi nhận hàng
- Thông báo trạng thái đơn hàng
- Scheduled notifications

---

## 🔧 CHỨC NĂNG CHÍNH - ADMIN DASHBOARD

### Admin Screens:
- `admin_shell.dart` - Layout admin shell
- `admin_login_screen.dart` - Đăng nhập admin
- `admin_dashboard_screen.dart` - Dashboard thống kê
- `admin_products_screen.dart` - Quản lý sản phẩm
- `admin_orders_screen.dart` - Quản lý đơn hàng
- `admin_users_screen.dart` - Quản lý người dùng
- `admin_coupons_screen.dart` - Quản lý mã giảm giá
- `admin_reviews_screen.dart` - Quản lý đánh giá
- `admin_carts_screen.dart` - Xem giỏ hàng khách hàng
- `admin_reports_screen.dart` - Báo cáo thống kê

### 1. 📊 DASHBOARD THỐNG KÊ
**Tính năng:**
- Tổng doanh thu
- Số đơn hàng
- Số lượng sản phẩm bán chạy
- Biểu đồ doanh thu theo thời gian (fl_chart)
- Thống kê top sản phẩm
- Thống kê top khách hàng

### 2. 📦 QUẢN LÝ SẢN PHẨM
**Tính năng:**
- CRUD sản phẩm (Create, Read, Update, Delete)
- Upload ảnh lên Cloudinary (tối đa nhiều ảnh)
- Quản lý thông số kỹ thuật (RAM, ROM, Chip, Pin, Màn hình, Camera)
- Quản lý màu sắc sản phẩm
- Đặt sản phẩm nổi bật (Featured)
- Quản lý tình trạng (Mới, Like New, Đã dùng)
- Lọc và tìm kiếm sản phẩm

### 3. 📋 QUẢN LÝ ĐƠN HÀNG
**Tính năng:**
- Xem danh sách tất cả đơn hàng
- Cập nhật trạng thái đơn hàng:
  - pending → confirmed → processing → shipping → delivered
  - Hoặc hủy đơn
- Xem chi tiết đơn hàng
- Xem địa chỉ giao hàng
- Xem thông tin thanh toán
- Lọc theo trạng thái, ngày tháng

### 4. 👥 QUẢN LÝ NGƯỜI DÙNG
**Tính năng:**
- Xem danh sách người dùng
- Khóa/mở khóa tài khoản
- Xem thông tin chi tiết người dùng
- Xem lịch sử đơn hàng của user

### 5. 💰 QUẢN LÝ MÃ GIẢM GIÁ (COUPON)
**Tính năng:**
- Tạo coupon với điều kiện áp dụng
- Loại giảm giá: % hoặc số tiền cố định
- Giới hạn số lần sử dụng
- Thiết lập ngày hết hạn
- Áp dụng cho tất cả sản phẩm hoặc sản phẩm cụ thể

**Coupon Model:**
```dart
CouponModel {
  code, discountType: 'percentage' | 'fixed',
  discountValue, maxDiscount, minOrderValue,
  usageLimit, usedCount, validFrom, validUntil,
  isActive, applicableProducts
}
```

### 6. ⭐ QUẢN LÝ ĐÁNH GIÁ (REVIEW MODERATION)
**Tính năng:**
- Xem tất cả đánh giá
- Xem đánh giá bị flag (cần kiểm duyệt)
- Duyệt đánh giá (approve)
- Từ chối đánh giá (reject)
- Xóa đánh giá
- Xem phân tích cảm xúc (ABSA)
- Xem kết quả content moderation
- Lọc theo trạng thái (pending, approved, rejected)

**Moderation Categories:**
- Harassment / Threatening
- Hate / Hate Threatening
- Illicit / Illicit Violent
- Self-harm / Self-harm Intent / Self-harm Instructions
- Sexual / Sexual Minors
- Violence / Violence Graphic

### 7. 🛒 QUẢN LÝ GIỎ HÀNG KHÁCH HÀNG
**Tính năng:**
- Xem giỏ hàng của tất cả người dùng
- Xem sản phẩm trong giỏ hàng
- Lọc theo user

### 8. 📈 BÁO CÁO THỐNG KÊ
**Tính năng:**
- Xuất báo cáo doanh thu (CSV, Excel)
- Thống kê theo ngày/tháng/năm
- Thống kê sản phẩm bán chạy
- Thống kê doanh thu theo sản phẩm
- Export dữ liệu với file export service

---

## 💳 THANH TOÁN - MOMO INTEGRATION

### MoMo Service (Frontend)
- `lib/core/services/momo_service.dart`

### MoMo Service (Backend)
- `backend/src/services/momoService.js`

**Tính năng:**
- Tạo yêu cầu thanh toán MoMo
- Kiểm tra trạng thái thanh toán
- Deep link callback sau khi thanh toán
- URL Launcher mở MoMo app
- App Links nhận callback

**Payment Status:**
```dart
enum PaymentStatus {
  pending,      // Chờ thanh toán
  processing,   // Đang xử lý
  success,      // Thành công
  failed,       // Thất bại
  cancelled,    // Đã hủy
  refunded      // Đã hoàn tiền
}
```

**MoMo Configuration (Sandbox):**
```
MOMO_PARTNER_CODE: MOMO
MOMO_ACCESS_KEY: F8BBA842ECF85
MOMO_SECRET_KEY: K951B6PE1waDMi640xX08PD3vg6EkVlz
MOMO_ENDPOINT: https://test-payment.momo.vn/v2/gateway/api
```

---

## 🔌 API ENDPOINTS

### Auth
- `POST /api/auth/register` - Đăng ký
- `POST /api/auth/login` - Đăng nhập
- `POST /api/auth/forgot-password` - Quên mật khẩu
- `POST /api/auth/reset-password` - Đặt lại mật khẩu
- `GET /api/auth/me` - Lấy thông tin user

### Products
- `GET /api/products` - Danh sách sản phẩm (hỗ trợ search, filter, pagination)
- `GET /api/products/search?q=keyword` - Tìm kiếm server-side
- `GET /api/products/:id` - Chi tiết sản phẩm
- `GET /api/products/:id/reviews` - Đánh giá sản phẩm

### Cart
- `GET /api/cart` - Xem giỏ hàng
- `POST /api/cart/add` - Thêm vào giỏ
- `PUT /api/cart/update` - Cập nhật số lượng
- `DELETE /api/cart/remove/:productId` - Xóa sản phẩm

### Orders
- `POST /api/orders` - Tạo đơn hàng
- `GET /api/orders` - Lịch sử đơn hàng
- `GET /api/orders/:id` - Chi tiết đơn hàng
- `PUT /api/orders/:id/cancel` - Hủy đơn hàng

### Reviews
- `POST /api/reviews` - Tạo đánh giá (tự động phân tích sentiment ABSA)
- `GET /api/reviews/product/:productId` - Lấy đánh giá theo sản phẩm
- `GET /api/reviews/my-reviews` - Lấy đánh giá của user
- `PUT /api/reviews/:id` - Cập nhật đánh giá
- `DELETE /api/reviews/:id` - Xóa đánh giá
- `POST /api/reviews/:id/like` - Like đánh giá

### User Profile
- `GET /api/auth/profile` - Lấy profile với địa chỉ
- `POST /api/auth/addresses` - Thêm địa chỉ
- `PUT /api/auth/addresses/:id` - Cập nhật địa chỉ
- `DELETE /api/auth/addresses/:id` - Xóa địa chỉ

### Admin
- `GET /api/admin/dashboard` - Thống kê tổng quan
- `GET/POST/PUT/DELETE /api/admin/products` - CRUD sản phẩm
- `GET/PUT /api/admin/orders` - Quản lý đơn hàng
- `GET/PUT/DELETE /api/admin/users` - Quản lý users
- `GET/POST/PUT/DELETE /api/admin/coupons` - Quản lý mã giảm giá
- `GET /api/admin/reports/*` - Báo cáo doanh thu

### Admin - Review Moderation
- `GET /api/admin/reviews` - Danh sách tất cả đánh giá
- `GET /api/admin/reviews/flagged` - Đánh giá bị flag (cần kiểm duyệt)
- `GET /api/admin/reviews/moderation-categories` - Danh mục vi phạm
- `PUT /api/admin/reviews/:id/approve` - Duyệt đánh giá
- `PUT /api/admin/reviews/:id/moderation/approve` - Duyệt sau kiểm duyệt
- `PUT /api/admin/reviews/:id/moderation/reject` - Từ chối đánh giá
- `DELETE /api/admin/reviews/:id` - Xóa đánh giá

### Upload
- `POST /api/upload/image` - Upload 1 ảnh lên Cloudinary
- `POST /api/upload/images` - Upload nhiều ảnh
- `POST /api/upload/review-images` - Upload ảnh đánh giá

### Payments
- `POST /api/payments/momo/create` - Tạo thanh toán MoMo
- `GET /api/payments/momo/status/:requestId` - Kiểm tra trạng thái
- `POST /api/payments/momo/ipn` - MoMo IPN callback

---

## 🗄️ DATABASE MODELS (MongoDB/Mongoose)

### User Model
```javascript
User {
  name, email, password, avatar,
  addresses: [{
    fullName, phone, province, district, ward, streetAddress, isDefault
  }],
  role: 'user' | 'admin',
  isLocked, createdAt, updatedAt
}
```

### Product Model
```javascript
Product {
  name, brand, description,
  price, originalPrice, images,
  specs: {ram, rom, chip, battery, screen, camera},
  colors: [{name, code, image}],
  condition: 'new' | 'likenew' | 'used',
  warranty, stock, rating, reviewCount,
  isFeatured, tags, isActive, createdAt, updatedAt
}
```

### Cart Model
```javascript
Cart {
  user, product, quantity,
  color: {name, code}, createdAt, updatedAt
}
```

### Order Model
```javascript
Order {
  user, orderNumber,
  items: [{product, name, brand, image, color, quantity, price}],
  shippingAddress: {fullName, phone, province, district, ward, streetAddress},
  paymentMethod, payment, paymentStatus,
  subtotal, shippingFee, tax, discount, coupon, total,
  status: 'pending' | 'confirmed' | 'processing' | 'shipping' | 'delivered' | 'cancelled',
  note, cancelReason,
  confirmedAt, processingAt, shippingAt, deliveredAt, cancelledAt,
  createdAt, updatedAt
}
```

### Payment Model
```javascript
Payment {
  order, user, method: 'COD' | 'MoMo' | 'VNPay' | 'ZaloPay' | 'Card',
  amount, requestId, momoOrderId, transId,
  payUrl, deeplink, qrCodeUrl,
  status: 'pending' | 'paid' | 'failed' | 'refunded',
  resultCode, message, createdAt, updatedAt
}
```

### Review Model
```javascript
Review {
  user, product, rating, title, text, images,
  isVerifiedPurchase, likes, isActive,
  sentimentAnalysis: [{aspect, sentiment, confidence, scores, aspectOnly}],
  overallSentiment: 'positive' | 'negative' | 'neutral' | 'mixed',
  isFlagged, moderationStatus: 'pending' | 'approved' | 'rejected',
  moderationResult: {id, model, flagged, categories, categoryScores, checkedAt},
  moderationNote,
  createdAt, updatedAt
}
```

### Coupon Model
```javascript
Coupon {
  code, discountType: 'percentage' | 'fixed',
  discountValue, maxDiscount, minOrderValue,
  usageLimit, usedCount,
  validFrom, validUntil, isActive,
  applicableProducts: [productId],
  createdAt, updatedAt
}
```

---

## 🔐 SECURITY & MIDDLEWARE

### Authentication
- JWT Token authentication
- Protected routes với `auth` middleware
- Admin-only routes với `admin` middleware

### Content Moderation
- Automatic content moderation for reviews
- Detect inappropriate content categories
- Flag reviews requiring manual moderation

### Input Validation
- Express-validator cho backend
- Client-side validation cho Flutter

---

## 🎨 UI/UX FEATURES

### Theme
- Dark mode / Light mode
- Custom color scheme
- Google Fonts integration

### Notifications
- flutter_local_notifications
- Permission handler
- Timezone support

### Icons
- Material Icons
- Cupertino Icons (iOS)

### Charts
- fl_chart cho admin dashboard

---

## 📦 DEPENDENCIES

### Flutter Dependencies
- **State Management**: provider ^6.1.2
- **HTTP Client**: dio ^5.4.0
- **Storage**: shared_preferences ^2.2.2, flutter_secure_storage ^9.0.0
- **Environment**: flutter_dotenv ^5.1.0
- **Images**: cached_network_image ^3.3.1, image_picker ^1.0.7
- **Utilities**: intl ^0.19.0, fl_chart ^1.1.1, google_fonts ^6.3.2
- **Security**: crypto ^3.0.3
- **Notifications**: flutter_local_notifications ^18.0.1, timezone ^0.10.0, permission_handler ^11.3.1
- **Payment**: url_launcher ^6.2.6, app_links ^6.4.0

### Backend Dependencies
- **Runtime**: Node.js 18+
- **Framework**: Express.js
- **Database**: MongoDB + Mongoose
- **Authentication**: jsonwebtoken
- **Image Storage**: Cloudinary, multer
- **Email**: Nodemailer
- **Validation**: express-validator

---

## 🌐 ENVIRONMENT VARIABLES

### Frontend (.env)
```
API_BASE_URL=http://192.168.1.16:3000/api
CLOUDINARY_CLOUD_NAME=drpxqclmg
```

### Backend (.env)
```
MONGODB_URI=mongodb+srv://...
JWT_SECRET=...
JWT_EXPIRES_IN=7d
JWT_REFRESH_EXPIRES_IN=30d
CLOUDINARY_CLOUD_NAME=drpxqclmg
CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...
PORT=3000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
MOMO_PARTNER_CODE=MOMO
MOMO_ACCESS_KEY=F8BBA842ECF85
MOMO_SECRET_KEY=K951B6PE1waDMi640xX08PD3vg6EkVlz
MOMO_ENDPOINT=https://test-payment.momo.vn/v2/gateway/api
MOMO_REDIRECT_URL=veritashop://momo-return
MOMO_IPN_URL=https://mobile.honeysocial.click/api/payments/momo/ipn
```

---

## 👥 TÀI KHOẢN MẶC ĐỊNH (SEED DATA)

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@veritashop.com | Admin@123 |
| Customer | user@veritashop.com | User@123 |

---

## 🚀 CÀI ĐẶT & CHẠY DỰ ÁN

### Backend
```bash
cd backend
npm install
npm run seed    # Seed database với dữ liệu mẫu
npm run dev    # Chạy backend tại http://localhost:3000
```

### Flutter
```bash
flutter pub get
flutter run -d chrome    # Web
flutter run -d android    # Android
flutter run -d ios        # iOS (macOS only)
```

---

## 📝 GHI CHÚ QUAN TRỌNG

### Voice Search
- Tính năng Voice Search đã bị XÓA khỏi dự án
- `voice_search_service.dart` vẫn còn tồn tại nhưng không được sử dụng

### ABSA (Aspect-Based Sentiment Analysis)
- Phân tích cảm xúc theo từng khía cạnh của đánh giá
- Tự động phân tích khi tạo review mới
- Hiển thị kết quả cho cả customer và admin

### Content Moderation
- Tự động phát hiện nội dung không phù hợp
- Admin phải duyệt/từ chối trước khi hiển thị
- Các categories: harassment, hate, illicit, self-harm, sexual, violence

### MoMo Payment
- Chỉ hỗ trợ sandbox environment
- Sử dụng deep link để mở MoMo app
- Callback URL: `veritashop://momo-return`

### Direct Checkout
- Có thể mua trực tiếp từ product detail mà không cần vào giỏ hàng
- Tính năng mới được thêm vào commit gần đây

---

## 🔄 QUY TRÌNH ĐẶT HÀNG

1. **Browsing** → Xem danh sách/tìm kiếm sản phẩm
2. **Product Detail** → Xem chi tiết, chọn màu, thêm vào giỏ hoặc mua ngay
3. **Cart** (optional) → Review giỏ hàng
4. **Checkout** → Chọn địa chỉ, thanh toán, áp dụng coupon
5. **Payment** → COD hoặc MoMo
6. **Order Created** → Đơn hàng được tạo, trạng thái "pending"
7. **Order Processing** → Admin xác nhận → xử lý → giao hàng
8. **Delivery** → Đơn hàng được giao
9. **Review** → Khách hàng đánh giá sản phẩm (ABSA analysis)
10. **Moderation** → Admin duyệt đánh giá

---

## 📱 SCREEN FLOW - CUSTOMER

**Main Flow:**
```
Splash → Onboarding → Login/Register → Home
```

**Home Features:**
- Banner/Featured products
- Categories
- Trending products
- Search bar

**Product Discovery:**
```
Home → Product List → Product Detail → Add to Cart / Buy Now
```

**Checkout Flow:**
```
Product Detail / Cart → Checkout → Address → Payment → Payment Processing → Order Success
```

**User Account:**
```
Profile → Edit Profile / Address / Orders / Settings / Logout
```

**Reviews:**
```
Product Detail → Reviews → Write Review (with images) → ABSA Analysis → Moderation
```

---

## 💡 KEY INSIGHTS

1. **Multi-language**: Giao diện hoàn toàn tiếng Việt
2. **Mobile-first**: Thiết kế cho mobile app chính
3. **ABS-First**: Sử dụng ABS (Aspect-Based Sentiment Analysis) cho reviews
4. **Content Safety**: Content moderation tự động cho reviews
5. **Payment Integration**: MoMo sandbox integration với deep links
6. **Cloud Storage**: Cloudinary cho tất cả hình ảnh
7. **Admin Dashboard**: Full CRUD operations cho admin
8. **Local Notifications**: Thông báo local cho order updates
9. **Pin Lock**: Bảo mật thêm lớp với PIN
10. **Direct Checkout**: Mua ngay từ product detail

---

## 🎯 FUTURE ENHANCEMENTS (POTENTIAL)

- Multi-language support (English)
- Push notifications (Firebase Cloud Messaging)
- Analytics integration (Firebase Analytics)
- Payment gateways: VNPay, ZaloPay
- Real-time chat support
- Product comparison
- Wishlist sharing
- Order tracking with map
- Voice search (đã bị xóa)
- Social login (Google, Facebook)

---

## 📚 THAM KHẢO

- **README.md**: Tài liệu chính của dự án
- **CLAUDE.md**: Coding guidelines
- **AGENTS.md**: OpenSpec instructions
- **MOMO.md**: MoMo integration docs
- **pubspec.yaml**: Flutter dependencies
- **backend/package.json**: Backend dependencies

---

*Memory Bank được tạo lúc: 2026-01-05*
*Cập nhật lần cuối: Theo dõi git commits*
