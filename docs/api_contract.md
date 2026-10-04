# KantinKampus API Contract

## 1. API Overview

API Contract ini merupakan spesifikasi RESTful API untuk aplikasi KantinKampus.

API digunakan untuk mendukung proses:

- autentikasi pengguna;
- melihat profil pengguna;
- melihat informasi kantin;
- melihat kategori menu;
- melihat daftar menu;
- melihat pilihan tambahan menu;
- membuat pesanan;
- melihat riwayat pesanan;
- melihat detail pesanan;
- melakukan pembayaran;
- memantau status antrean;
- mengelola menu oleh pengelola kantin;
- mengelola status pesanan oleh pengelola kantin.

API dirancang berdasarkan ERD KantinKampus dan menggunakan format JSON yang konsisten.

---

## 2. Base URL

```text
/api/v1

---

## 3. Authentication

Endpoint yang membutuhkan autentikasi menggunakan Bearer Token.

Format header:

```http
Authorization: Bearer {access_token}
Content-Type: application/json

---

## 4. Standard Response

Semua endpoint API menggunakan struktur response JSON yang konsisten.

### Success Response

```json
{
  "status": "success",
  "message": "Request berhasil",
  "data": {}
}

### Error Response

```json
{
  "status": "error",
  "message": "Request gagal",
  "errors": {}
}
```

---

## 5. HTTP Status Code

API menggunakan HTTP status code berikut:

| Status Code | Keterangan |
|---|---|
| 200 | Request berhasil |
| 201 | Data berhasil dibuat |
| 400 | Request tidak valid |
| 401 | Tidak terautentikasi |
| 403 | Tidak memiliki izin |
| 404 | Data tidak ditemukan |
| 422 | Validasi input gagal |
| 500 | Kesalahan server |

---

## 6. API Endpoints

### 6.1 Login

**POST** `/auth/login`

**Role:** Public

**Request Body:**

```json
{
  "email": "mahasiswa@kampus.ac.id",
  "password": "password123"
}
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Login berhasil",
  "data": {
    "access_token": "eyJhbGciOi...",
    "user": {
      "id": 1,
      "nim": "210882001",
      "name": "Muhammad Daffa",
      "email": "mahasiswa@kampus.ac.id",
      "faculty": "Fakultas Ilmu Komputer"
    }
  }
}
```

**Error Response:**

- `401` — Email atau password salah.
- `422` — Email atau password wajib diisi.

```json
{
  "status": "error",
  "message": "Email atau password salah",
  "errors": {}
}
```

---

### 6.2 Get Current User

**GET** `/users/me`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Data pengguna berhasil diambil",
  "data": {
    "id": 1,
    "nim": "210882001",
    "name": "Muhammad Daffa",
    "email": "mahasiswa@kampus.ac.id",
    "faculty": "Fakultas Ilmu Komputer",
    "balance": 48500
  }
}
```

**Error Response:**

- `401` — Token tidak valid atau belum login.
- `404` — Data pengguna tidak ditemukan.

```json
{
  "status": "error",
  "message": "Pengguna tidak ditemukan",
  "errors": {}
}
```

---

### 6.3 Get Canteens

**GET** `/canteens`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Data kantin berhasil diambil",
  "data": [
    {
      "id": 1,
      "name": "Kantin Pusat GKU",
      "location": "Gedung Kuliah Bersama",
      "building": "GKU Lt. 1",
      "status": "open",
      "queue_density": 88,
      "estimated_wait_minutes": 25
    }
  ]
}
```

**Error Response:**

- `401` — Pengguna belum terautentikasi.
- `404` — Data kantin tidak ditemukan.

```json
{
  "status": "error",
  "message": "Data kantin tidak ditemukan",
  "errors": {}
}
```

---

### 6.4 Get Categories

**GET** `/categories`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Data kategori berhasil diambil",
  "data": [
    {
      "id": 1,
      "name": "Aneka Nasi"
    },
    {
      "id": 2,
      "name": "Mie & Bakso"
    },
    {
      "id": 3,
      "name": "Minuman"
    }
  ]
}
```

**Error Response:**

- `401` — Pengguna belum terautentikasi.
- `404` — Data kategori tidak ditemukan.

```json
{
  "status": "error",
  "message": "Data kategori tidak ditemukan",
  "errors": {}
}
```

---

### 6.5 Get Menus

**GET** `/menus`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Query Parameters:** Opsional.

```text
?canteen_id=1&category_id=1
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Data menu berhasil diambil",
  "data": [
    {
      "id": 1,
      "canteen_id": 1,
      "category_id": 1,
      "name": "Nasi Goreng Spesial",
      "description": "Nasi goreng dengan telur mata sapi dan topping pilihan",
      "price": 15000,
      "image_url": "https://example.com/nasi-goreng.jpg",
      "rating": 4.8,
      "is_available": true
    },
    {
      "id": 2,
      "canteen_id": 1,
      "category_id": 1,
      "name": "Ayam Geprek Sambal Bawang",
      "description": "Ayam crispy dengan sambal bawang",
      "price": 15000,
      "image_url": "https://example.com/ayam-geprek.jpg",
      "rating": 4.9,
      "is_available": true
    }
  ]
}
```

**Error Response:**

- `401` — Pengguna belum terautentikasi.
- `404` — Data menu tidak ditemukan.

```json
{
  "status": "error",
  "message": "Data menu tidak ditemukan",
  "errors": {}
}
```

---

### 6.6 Get Menu Detail

**GET** `/menus/{id}`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Path Parameter:**

```text
id = ID menu
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Detail menu berhasil diambil",
  "data": {
    "id": 1,
    "canteen_id": 1,
    "category_id": 1,
    "name": "Nasi Goreng Spesial",
    "description": "Nasi goreng racikan bumbu khas Nusantara dengan telur mata sapi, ayam, bakso, acar dan kerupuk.",
    "price": 15000,
    "image_url": "https://example.com/nasi-goreng.jpg",
    "rating": 4.8,
    "is_available": true
  }
}
```

**Error Response:**

- `401` — Pengguna belum terautentikasi.
- `404` — Menu tidak ditemukan.

```json
{
  "status": "error",
  "message": "Menu tidak ditemukan",
  "errors": {}
}
```

---

---

### 6.7 Get Menu Options

**GET** `/menus/{id}/options`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Path Parameter:**

```text
id = ID menu
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Pilihan menu berhasil diambil",
  "data": [
    {
      "id": 1,
      "menu_id": 1,
      "option_group": "Tingkat Kepedasan",
      "option_name": "Pedas",
      "additional_price": 0,
      "is_available": true
    },
    {
      "id": 2,
      "menu_id": 1,
      "option_group": "Tambahan",
      "option_name": "Telur",
      "additional_price": 3000,
      "is_available": true
    }
  ]
}
```

**Error Response:**

- `401` — Pengguna belum terautentikasi.
- `404` — Menu atau pilihan menu tidak ditemukan.

```json
{
  "status": "error",
  "message": "Pilihan menu tidak ditemukan",
  "errors": {}
}
```

---

---

### 6.8 Create Order

**POST** `/orders`

**Role:** Mahasiswa

**Authentication:** Required

**Request Body:**

```json
{
  "canteen_id": 1,
  "items": [
    {
      "menu_id": 1,
      "quantity": 2,
      "options": [
        {
          "menu_option_id": 1
        },
        {
          "menu_option_id": 2
        }
      ]
    }
  ]
}
```

**Success Response — 201:**

```json
{
  "status": "success",
  "message": "Pesanan berhasil dibuat",
  "data": {
    "id": 1,
    "user_id": 1,
    "canteen_id": 1,
    "status": "pending",
    "total_amount": 23000,
    "created_at": "2026-10-04T09:30:00"
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Pesanan gagal dibuat",
  "errors": {}
}
```

**HTTP Status:**

- `201` — Pesanan berhasil dibuat.
- `400` — Data pesanan tidak valid.
- `401` — Pengguna belum terautentikasi.
- `404` — Menu atau kantin tidak ditemukan.
- `422` — Validasi data gagal.
- `500` — Kesalahan server.

---

### 6.9 Get Order History

**GET** `/orders`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Query Parameters:** Opsional.

```text
status = Status pesanan
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Riwayat pesanan berhasil diambil",
  "data": [
    {
      "id": 1,
      "user_id": 1,
      "canteen_id": 1,
      "status": "completed",
      "total_amount": 23000,
      "created_at": "2026-10-04T09:30:00"
    }
  ]
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Riwayat pesanan gagal diambil",
  "errors": {}
}
```

**HTTP Status:**

- `200` — Riwayat pesanan berhasil diambil.
- `401` — Pengguna belum terautentikasi.
- `404` — Data pesanan tidak ditemukan.
- `500` — Kesalahan server.

---

### 6.10 Get Order Detail

**GET** `/orders/{id}`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Path Parameter:**

```text
id = ID pesanan
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Detail pesanan berhasil diambil",
  "data": {
    "id": 1,
    "user_id": 1,
    "canteen_id": 1,
    "status": "completed",
    "total_amount": 23000,
    "created_at": "2026-10-04T09:30:00",
    "items": [
      {
        "id": 1,
        "menu_id": 1,
        "menu_name": "Nasi Goreng Spesial",
        "quantity": 2,
        "price": 10000,
        "subtotal": 20000,
        "options": [
          {
            "menu_option_id": 1,
            "option_name": "Pedas",
            "additional_price": 0
          },
          {
            "menu_option_id": 2,
            "option_name": "Telur",
            "additional_price": 3000
          }
        ]
      }
    ]
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Detail pesanan gagal diambil",
  "errors": {}
}
```

**HTTP Status:**

- `200` — Detail pesanan berhasil diambil.
- `401` — Pengguna belum terautentikasi.
- `404` — Pesanan tidak ditemukan.
- `500` — Kesalahan server.

---

### 6.11 Create Payment

**POST** `/orders/{id}/payment`

**Role:** Mahasiswa

**Authentication:** Required

**Request Body:**

```json
{
  "payment_method": "cash"
}
```

**Path Parameter:**

```text
id = ID pesanan
```

**Success Response — 201:**

```json
{
  "status": "success",
  "message": "Pembayaran berhasil dibuat",
  "data": {
    "id": 1,
    "order_id": 1,
    "payment_method": "cash",
    "amount": 23000,
    "status": "pending",
    "paid_at": null
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Pembayaran gagal dibuat",
  "errors": {}
}
```

**HTTP Status:**

- `201` — Pembayaran berhasil dibuat.
- `400` — Data pembayaran tidak valid.
- `401` — Pengguna belum terautentikasi.
- `404` — Pesanan tidak ditemukan.
- `422` — Validasi data gagal.
- `500` — Kesalahan server.

---

### 6.12 Get Payment Status

**GET** `/orders/{id}/payment`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Path Parameter:**

```text
id = ID pesanan
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Status pembayaran berhasil diambil",
  "data": {
    "id": 1,
    "order_id": 1,
    "payment_method": "cash",
    "amount": 23000,
    "status": "paid",
    "paid_at": "2026-10-04T09:35:00"
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Status pembayaran gagal diambil",
  "errors": {}
}
```

**HTTP Status:**

- `200` — Status pembayaran berhasil diambil.
- `401` — Pengguna belum terautentikasi.
- `404` — Pembayaran atau pesanan tidak ditemukan.
- `500` — Kesalahan server.

---

### 6.13 Get Queue Status

**GET** `/orders/{id}/queue`

**Role:** Mahasiswa / Pengelola Kantin

**Authentication:** Required

**Request Body:** Tidak ada.

**Path Parameter:**

```text
id = ID pesanan
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Status antrean berhasil diambil",
  "data": {
    "id": 1,
    "order_id": 1,
    "queue_number": 15,
    "status": "waiting",
    "estimated_wait_minutes": 20
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Status antrean gagal diambil",
  "errors": {}
}
```

**HTTP Status:**

- `200` — Status antrean berhasil diambil.
- `401` — Pengguna belum terautentikasi.
- `404` — Data antrean atau pesanan tidak ditemukan.
- `500` — Kesalahan server.

---

### 6.14 Update Order Status

**PATCH** `/orders/{id}/status`

**Role:** Pengelola Kantin

**Authentication:** Required

**Request Body:**

```json
{
  "status": "preparing"
}
```

**Path Parameter:**

```text
id = ID pesanan
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Status pesanan berhasil diperbarui",
  "data": {
    "id": 1,
    "status": "preparing",
    "updated_at": "2026-10-04T09:40:00"
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Status pesanan gagal diperbarui",
  "errors": {}
}
```

**HTTP Status:**

- `200` — Status pesanan berhasil diperbarui.
- `400` — Status pesanan tidak valid.
- `401` — Pengguna belum terautentikasi.
- `403` — Pengguna tidak memiliki izin.
- `404` — Pesanan tidak ditemukan.
- `422` — Validasi data gagal.
- `500` — Kesalahan server.

---

### 6.15 Create Menu

**POST** `/menus`

**Role:** Pengelola Kantin

**Authentication:** Required

**Request Body:**

```json
{
  "canteen_id": 1,
  "category_id": 1,
  "name": "Nasi Goreng Spesial",
  "description": "Nasi goreng dengan telur dan ayam",
  "price": 15000,
  "image_url": "https://example.com/nasi-goreng.jpg",
  "is_available": true
}
```

**Success Response — 201:**

```json
{
  "status": "success",
  "message": "Menu berhasil dibuat",
  "data": {
    "id": 10,
    "canteen_id": 1,
    "category_id": 1,
    "name": "Nasi Goreng Spesial",
    "description": "Nasi goreng dengan telur dan ayam",
    "price": 15000,
    "image_url": "https://example.com/nasi-goreng.jpg",
    "rating": 0,
    "is_available": true
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Menu gagal dibuat",
  "errors": {}
}
```

**HTTP Status:**

- `201` — Menu berhasil dibuat.
- `400` — Data menu tidak valid.
- `401` — Pengguna belum terautentikasi.
- `403` — Pengguna tidak memiliki izin.
- `404` — Kantin atau kategori tidak ditemukan.
- `422` — Validasi data gagal.
- `500` — Kesalahan server.

---

### 6.16 Update Menu

**PATCH** `/menus/{id}`

**Role:** Pengelola Kantin

**Authentication:** Required

**Request Body:**

```json
{
  "name": "Nasi Goreng Spesial",
  "description": "Nasi goreng dengan telur, ayam, dan sayuran",
  "price": 17000,
  "is_available": true
}
```

**Path Parameter:**

```text
id = ID menu
```

**Success Response — 200:**

```json
{
  "status": "success",
  "message": "Menu berhasil diperbarui",
  "data": {
    "id": 10,
    "canteen_id": 1,
    "category_id": 1,
    "name": "Nasi Goreng Spesial",
    "description": "Nasi goreng dengan telur, ayam, dan sayuran",
    "price": 17000,
    "image_url": "https://example.com/nasi-goreng.jpg",
    "rating": 4.8,
    "is_available": true
  }
}
```

**Error Response:**

```json
{
  "status": "error",
  "message": "Menu gagal diperbarui",
  "errors": {}
}
```

**HTTP Status:**

- `200` — Menu berhasil diperbarui.
- `400` — Data menu tidak valid.
- `401` — Pengguna belum terautentikasi.
- `403` — Pengguna tidak memiliki izin.
- `404` — Menu tidak ditemukan.
- `422` — Validasi data gagal.
- `500` — Kesalahan server.

---

---

## 7. Role-Permission Matrix

| Endpoint | Public | Mahasiswa | Pengelola Kantin |
|---|:---:|:---:|:---:|
| POST `/auth/login` | ✓ | - | - |
| GET `/users/me` | - | ✓ | ✓ |
| GET `/canteens` | - | ✓ | ✓ |
| GET `/categories` | - | ✓ | ✓ |
| GET `/menus` | - | ✓ | ✓ |
| GET `/menus/{id}` | - | ✓ | ✓ |
| GET `/menus/{id}/options` | - | ✓ | ✓ |
| POST `/orders` | - | ✓ | - |
| GET `/orders` | - | ✓ | ✓ |
| GET `/orders/{id}` | - | ✓ | ✓ |
| POST `/orders/{id}/payment` | - | ✓ | - |
| GET `/orders/{id}/payment` | - | ✓ | ✓ |
| GET `/orders/{id}/queue` | - | ✓ | ✓ |
| PATCH `/orders/{id}/status` | - | - | ✓ |
| POST `/menus` | - | - | ✓ |
| PATCH `/menus/{id}` | - | - | ✓ |

---

## 8. Error Handling

API menggunakan struktur error yang konsisten untuk setiap endpoint.

### 8.1 Unauthorized — 401

Digunakan ketika pengguna belum login atau access token tidak valid.

```json
{
  "status": "error",
  "message": "Unauthorized",
  "errors": {
    "authentication": "Token tidak valid atau telah kedaluwarsa"
  }
}
```

### 8.2 Forbidden — 403

Digunakan ketika pengguna sudah login tetapi tidak memiliki hak akses terhadap endpoint.

```json
{
  "status": "error",
  "message": "Forbidden",
  "errors": {
    "permission": "Anda tidak memiliki izin untuk melakukan tindakan ini"
  }
}
```

### 8.3 Not Found — 404

Digunakan ketika resource yang diminta tidak ditemukan.

```json
{
  "status": "error",
  "message": "Data tidak ditemukan",
  "errors": {
    "resource": "Resource yang diminta tidak ditemukan"
  }
}
```

### 8.4 Validation Error — 422

Digunakan ketika data yang dikirim tidak memenuhi aturan validasi.

```json
{
  "status": "error",
  "message": "Validasi gagal",
  "errors": {
    "field": "Data yang diberikan tidak valid"
  }
}
```

### 8.5 Bad Request — 400

Digunakan ketika request tidak valid atau tidak dapat diproses.

```json
{
  "status": "error",
  "message": "Request tidak valid",
  "errors": {
    "request": "Data request tidak dapat diproses"
  }
}
```

### 8.6 Internal Server Error — 500

Digunakan ketika terjadi kesalahan pada server.

```json
{
  "status": "error",
  "message": "Terjadi kesalahan pada server",
  "errors": {
    "server": "Silakan coba kembali beberapa saat lagi"
  }
}
```

---

## 9. Changelog

### Version 1.0.0

**Tanggal:** 4 Oktober 2026

Perubahan:

- Menambahkan API Contract awal untuk aplikasi KantinKampus.
- Menambahkan autentikasi pengguna.
- Menambahkan endpoint profil pengguna.
- Menambahkan endpoint informasi kantin.
- Menambahkan endpoint kategori dan menu.
- Menambahkan endpoint pilihan menu.
- Menambahkan endpoint pembuatan dan pengelolaan pesanan.
- Menambahkan endpoint pembayaran.
- Menambahkan endpoint status antrean.
- Menambahkan endpoint pengelolaan menu oleh pengelola kantin.
- Menambahkan standard JSON response.
- Menambahkan HTTP status code.
- Menambahkan error handling.
- Menambahkan role-permission matrix.
