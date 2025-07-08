

<h1 align="center">🏸 Badminton Shop - Hệ thống thương mại điện tử bán linh kiện cầu lông</h1>
<p align="center">
  <img src="https://img.shields.io/badge/Stack-MERN-blue" alt="MERN Stack"/>
  <img src="https://img.shields.io/badge/Frontend-Next.js-blueviolet" alt="Next.js"/>
  <img src="https://img.shields.io/badge/Backend-Express-green" alt="Express"/>
  <img src="https://img.shields.io/badge/CI%2FCD-Jenkins-orange" alt="Jenkins"/>
  <img src="https://img.shields.io/badge/Cloud-AWS-yellow" alt="AWS"/>
</p>

<p align="center">
  <a href="./CICD_SETUP.md" style="font-size:1.1em;font-weight:bold;">
    <img src="https://img.shields.io/badge/Xem%20hướng%20dẫn%20CI%2FCD%20và%20triển%20khai-blue?logo=jenkins&logoColor=white" alt="CI/CD Setup"/>
  </a>
</p>


## 📋 Mô tả dự án

Badminton Shop là một hệ thống thương mại điện tử hoàn chỉnh chuyên bán linh kiện cầu lông, được xây dựng với công nghệ MERN Stack (MongoDB, Express.js, React, Node.js).

## ✨ Tính năng chính

### Backend
- ✅ API RESTful với Express.js
- ✅ Kết nối MongoDB Atlas
- ✅ Xác thực JWT
- ✅ Upload hình ảnh với Cloudinary
- ✅ Quản lý sản phẩm (CRUD)
- ✅ Quản lý giỏ hàng và đơn hàng
- ✅ Phân quyền admin/customer
- ✅ Validation và error handling

### Frontend
- ✅ Giao diện responsive với Tailwind CSS
- ✅ Quản lý state với React Context
- ✅ Tích hợp React Query cho data fetching
- ✅ Trang chủ với hero section và featured products
- ✅ Trang sản phẩm với filter và search
- ✅ Giỏ hàng và checkout
- ✅ Trang admin cho quản lý sản phẩm
- ✅ Authentication và authorization

## 🛠️ Công nghệ sử dụng

### Backend
- **Node.js** - Runtime environment
- **Express.js** - Web framework
- **MongoDB** - Database
- **Mongoose** - ODM
- **JWT** - Authentication
- **bcryptjs** - Password hashing
- **Cloudinary** - Image upload
- **Multer** - File upload
- **Express Validator** - Input validation

### Frontend
- **Next.js** - React framework
- **TypeScript** - Type safety
- **Tailwind CSS** - Styling
- **React Query** - Data fetching
- **React Hook Form** - Form handling
- **React Icons** - Icons
- **Framer Motion** - Animations

## 📁 Cấu trúc dự án

```
badminton-web/
├── backend/
│   ├── models/
│   │   ├── User.js
│   │   ├── Product.js
│   │   ├── Cart.js
│   │   └── Order.js
│   ├── routes/
│   │   ├── auth.js
│   │   ├── products.js
│   │   ├── cart.js
│   │   ├── orders.js
│   │   └── upload.js
│   ├── middleware/
│   │   └── auth.js
│   ├── utils/
│   │   └── cloudinary.js
│   └── server.js
├── frontend/
│   ├── components/
│   │   ├── Layout/
│   │   │   ├── Header.tsx
│   │   │   └── Footer.tsx
│   │   └── Products/
│   │       └── ProductCard.tsx
│   ├── contexts/
│   │   ├── AuthContext.tsx
│   │   └── CartContext.tsx
│   ├── pages/
│   │   └── index.tsx
│   ├── styles/
│   │   └── globals.css
│   └── package.json
├── package.json
├── .env (tự tạo, không commit lên GitHub)
└── README.md
```


## 🚀 Hướng dẫn cài đặt & chạy trên máy cá nhân/IDE (Local Development)

### Yêu cầu hệ thống
- Node.js (v16 trở lên)
- npm hoặc yarn
- MongoDB Atlas account
- Cloudinary account

### Bước 1: Clone dự án
```bash
git clone <repository-url>
cd badminton-web
```

### Bước 2: Cài đặt dependencies
```bash
# Cài đặt dependencies cho backend
npm install

# Cài đặt dependencies cho frontend
cd frontend
npm install
cd ..
```


### Bước 3: Cấu hình môi trường

Tạo file `.env` trong thư mục gốc với nội dung mẫu như sau (không commit file này lên GitHub):

```env
# Server Configuration
PORT=5000
NODE_ENV=development

# MongoDB Atlas Connection
MONGODB_URI=your_mongodb_connection_string

# JWT Secret
JWT_SECRET=your_jwt_secret

# Encryption Key (64 hex characters for AES-256)
ENCRYPTION_KEY=your_64_char_encryption_key

# Cloudinary Configuration
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Frontend URL
FRONTEND_URL=http://localhost:3000
```

> **Lưu ý:** Không public file `.env` hoặc bất kỳ file nào chứa thông tin nhạy cảm lên GitHub. Đã loại bỏ file `env.example` khỏi repo để đảm bảo an toàn.

### Bước 4: Cấu hình MongoDB Atlas

1. Đăng ký tài khoản MongoDB Atlas
2. Tạo cluster mới
3. Tạo database user với quyền read/write
4. Lấy connection string và cập nhật vào file `.env`

### Bước 5: Cấu hình Cloudinary

1. Đăng ký tài khoản Cloudinary
2. Lấy Cloud Name, API Key, API Secret
3. Cập nhật vào file `.env`

### Bước 6: Chạy dự án

#### Chạy backend
```bash
# Development mode
npm run dev

# Production mode
npm start
```

#### Chạy frontend
```bash
cd frontend
npm run dev
```

#### Chạy cả backend và frontend
```bash
npm run dev-full
```

## 🌐 Truy cập ứng dụng

- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:5000
- **API Health Check**: http://localhost:5000/api/health

## 📚 API Endpoints

### Authentication
- `POST /api/auth/register` - Đăng ký
- `POST /api/auth/login` - Đăng nhập
- `GET /api/auth/profile` - Lấy thông tin profile
- `PUT /api/auth/profile` - Cập nhật profile

### Products
- `GET /api/products` - Lấy danh sách sản phẩm
- `GET /api/products/:id` - Lấy chi tiết sản phẩm
- `POST /api/products` - Tạo sản phẩm (Admin)
- `PUT /api/products/:id` - Cập nhật sản phẩm (Admin)
- `DELETE /api/products/:id` - Xóa sản phẩm (Admin)

### Cart
- `GET /api/cart` - Lấy giỏ hàng
- `POST /api/cart` - Thêm sản phẩm vào giỏ hàng
- `PUT /api/cart/:itemId` - Cập nhật số lượng
- `DELETE /api/cart/:itemId` - Xóa sản phẩm khỏi giỏ hàng

### Orders
- `POST /api/orders` - Tạo đơn hàng
- `GET /api/orders` - Lấy danh sách đơn hàng
- `GET /api/orders/:id` - Lấy chi tiết đơn hàng

### Upload
- `POST /api/upload/image` - Upload hình ảnh đơn
- `POST /api/upload/images` - Upload nhiều hình ảnh

## 👥 Tài khoản mẫu

### Admin
- Email: admin@badmintonshop.com
- Password: admin123

### Customer
- Email: customer@badmintonshop.com
- Password: customer123

## 🔧 Tính năng nâng cao

### Backend
- ✅ Rate limiting
- ✅ CORS configuration
- ✅ Security headers với Helmet
- ✅ Input validation
- ✅ Error handling middleware
- ✅ Image optimization với Cloudinary
- ✅ Pagination cho products và orders
- ✅ Search và filter sản phẩm
- ✅ Order status management
- ✅ Stock management

### Frontend
- ✅ Responsive design
- ✅ Loading states
- ✅ Error handling
- ✅ Toast notifications
- ✅ Form validation
- ✅ Image lazy loading
- ✅ Infinite scroll (có thể thêm)
- ✅ Dark mode (có thể thêm)



## 🔄 Lưu ý: Hướng dẫn khởi động lại website khi deploy production (xem chi tiết trong file CICD_SETUP.md)

Nếu bạn deploy trên server (EC2, VPS, máy chủ thật), khi máy chủ bị tắt hoặc khởi động lại, hãy xem hướng dẫn chi tiết khởi động lại website trong file `CICD_SETUP.md`.

Tóm tắt:
1. Đăng nhập SSH vào server, cd vào thư mục dự án.
2. Nếu container cũ còn, xóa trước: `docker rm -f badminton-web`
3. Chạy lại container:
   ```bash
   docker run -d --name badminton-web -p 80:80 -p 5000:5000 --env-file .env badminton-web:latest
   ```
4. Nếu cần build lại image:
   ```bash
   docker build -t badminton-web:latest .
   docker run -d --name badminton-web -p 80:80 -p 5000:5000 --env-file .env badminton-web:latest
   ```
5. Xem log: `docker logs badminton-web`
6. Truy cập lại web qua IP hoặc domain.

**Xem hướng dẫn chi tiết, các lưu ý bảo mật, backup, log... trong file CICD_SETUP.md**

## 🚀 Deployment


### Triển khai CI/CD với Jenkins trên AWS EC2

Xem hướng dẫn chi tiết trong file `CICD_SETUP.md` (đã tối ưu cho EC2 Free Tier, OpenJDK mới nhất, Docker, Jenkins, bảo mật biến môi trường, loại bỏ file nhạy cảm khi public).

**Các bước chính:**
1. Tạo EC2 Ubuntu 22.04 LTS Free Tier
2. Cài Jenkins, Docker, cấu hình quyền cho user Jenkins và ubuntu
3. Tạo các Jenkins credentials đúng tên biến môi trường trong `.env`
4. Đảm bảo `.gitignore` đã loại trừ `.env`, `node_modules`, `frontend/.next`, các file test/script không cần thiết
5. Đẩy code lên GitHub, pipeline Jenkins sẽ tự động build & deploy

> **Lưu ý:** Không sử dụng các file `.md` không cần thiết như `TROUBLESHOOTING.md`, `SECURITY.md` trong repo public.

## 🤝 Đóng góp

1. Fork dự án
2. Tạo feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Tạo Pull Request

## 📝 License

Dự án này được phát hành dưới MIT License.

## 📞 Liên hệ

- Email: info@badmintonshop.com
- Website: https://badmintonshop.com
- GitHub: [repository-url]

## 🙏 Cảm ơn

Cảm ơn bạn đã quan tâm đến dự án Badminton Shop! Nếu có bất kỳ câu hỏi nào, vui lòng liên hệ với chúng tôi. 