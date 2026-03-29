# Proof of Concept: Admin Access Indicator via Server Response

**Target:** Digital Library System (CTF)

**Category:** Information Disclosure / Authorization Misconfiguration

---

## 1. Deskripsi Challenge

Challenge meminta untuk menemukan:

> **Nilai apa yang menandakan akses Admin telah aktif?**

Hint yang diberikan adalah **response server**, sehingga fokus utama adalah menganalisis response dari server saat proses login berhasil.

---

## 2. Analisis Masalah

Aplikasi menggunakan endpoint berikut untuk login:

```
POST /backend/auth.php?action=login
```

Setelah login berhasil, server mengembalikan response dalam bentuk:

- **Response Body (JSON)**
- **Response Header (HTTP)**

Dari pengujian menggunakan browser dan Burp Suite, ditemukan bahwa indikator akses tidak hanya terdapat pada JSON, tetapi juga pada **HTTP Header**.

---

## 3. Langkah Penyelesaian (Exploitation)

### 1. Intercept Request Login

1. Buka aplikasi Digital Library System
2. Aktifkan Burp Suite (Intercept ON)
3. Lakukan login menggunakan kredensial yang valid:

```
librarian_admin : admin
```

---

### 2. Analisis Response Server

Perhatikan response yang dikirim oleh server setelah login:


