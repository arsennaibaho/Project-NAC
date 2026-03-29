# Proof of Concept: Admin Access Indicator via Server Response

**Target:** Digital Library System 

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
<img width="1598" height="899" alt="Image" src="https://github.com/user-attachments/assets/01747b5c-91c4-432e-9b3b-ecafd3fc48b4" />

---

### 3. Identifikasi Nilai Kunci

Dari response tersebut, ditemukan header penting:

```
X-Session-Access-Level: granted
```

Nilai "granted" menunjukkan bahwa akses telah diberikan oleh server, termasuk akses admin-level.

---

## 4. Solution

Untuk mencegah kerentanan seperti ini, langkah-langkah berikut perlu diterapkan:

1. **Hindari Mengungkap Informasi Sensitif di Header**  
   Jangan kirim informasi seperti: X-Session-Access-Level: granted

   Gunakan mekanisme internal server untuk kontrol akses

2. **Gunakan Session-Based Authorization**  
   Simpan status autentikasi di server (session), bukan di header atau cookie yang mudah dibaca
   
3. **Minimalisasi Informasi pada Response**  
   Response sebaiknya hanya berisi informasi yang diperlukan

   Hindari memberikan detail berlebih yang dapat membantu attacker

4. **Gunakan Token yang Aman (JWT / Session ID)**  
   Gunakan token yang tidak mengandung informasi sensitif secara langsung

   Pastikan token dienkripsi atau ditandatangani dengan benar

5. **Implementasi Principle of Least Privilege**  
   Berikan akses hanya sesuai kebutuhan user

   Jangan expose role atau privilege secara eksplisit ke client

6. **ISecure Cookie Configuration**  
   Gunakan atribut: HttpOnly, Secure & SameSite

   Contoh

   ```
   Set-Cookie: session_id=xyz; HttpOnly; Secure; SameSite=Strict
   ```

7. **Audit dan Logging**
   Monitor aktivitas login dan akses admin

   Deteksi anomali penggunaan akses

---

## 5. Flag

### Granted
<img width="661" height="154" alt="image" src="https://github.com/user-attachments/assets/08cf5580-06c5-477a-be68-58a12e648424" />
<img width="661" height="136" alt="image" src="https://github.com/user-attachments/assets/3f24bdf5-1fc2-432e-8649-aab002e9c3ac" />


---

### 6. CVSS Score
<img width="1438" height="784" alt="image" src="https://github.com/user-attachments/assets/3672d1b9-7005-46e8-b680-5800c1441ae6" />

