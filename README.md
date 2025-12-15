# Praktikum 12 – Autentikasi dan Session (PHP + XAMPP)

## Identitas

* Mata Kuliah: Pemrograman Web
* Praktikum: 12 (Autentikasi dan Session)
* Project: `lab11_php_oop`
* Web Server: XAMPP (Apache & MySQL)

---

## Tujuan Praktikum

1. Memahami konsep autentikasi (login & logout)
2. Memahami penggunaan session pada PHP
3. Mengimplementasikan login sederhana berbasis database

---

## Tools yang Digunakan

* XAMPP Control Panel
* Apache Web Server
* MySQL Database
* phpMyAdmin
* Visual Studio Code
* Browser (Chrome / Edge)

---

## Struktur Folder Project

```
lab11_php_oop/
├── index.php
├── config.php
├── class/
│   └── Database.php
├── module/
│   ├── home/
│   │   └── index.php
│   └── user/
│       ├── login.php
│       └── logout.php
└── template/
```

---

## Langkah 1 – Menjalankan XAMPP

1. Buka XAMPP Control Panel
2. Start **Apache** dan **MySQL**
3. Pastikan status berwarna hijau (Running)

---

## Langkah 2 – Membuat Database

1. Buka `http://localhost/phpmyadmin`
2. Klik **New**
3. Buat database dengan nama:

   ```
   latihan_oop
   ```

---

## Langkah 3 – Membuat Tabel Users

Jalankan query berikut di tab SQL:

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(255),
    nama VARCHAR(100)
);
```

---

## Langkah 4 – Insert Data User Admin

```sql
INSERT INTO users (username, password, nama)
VALUES ('admin', 'admin', 'Administrator');
```

---

## Langkah 5 – Router Utama (index.php)

File ini berfungsi sebagai pengatur halaman (routing).

```php
<?php
session_start();

$mod  = $_GET['mod']  ?? 'home';
$page = $_GET['page'] ?? 'index';

$file = "module/$mod/$page.php";

if (file_exists($file)) {
    include $file;
} else {
    echo "404";
}
```

---

## Langkah 6 – Halaman Home

File: `module/home/index.php`

```php
<h1>HOME</h1>

<?php if (!isset($_SESSION['is_login'])): ?>
    <a href="index.php?mod=user&page=login">Login</a>
<?php else: ?>
    <p>Selamat datang, ADMIN</p>
    <a href="index.php?mod=user&page=logout">Logout</a>
<?php endif; ?>
```

---

## Langkah 7 – Class Database

File: `class/Database.php`

```php
<?php
class Database {
    public $conn;

    public function __construct() {
        $this->conn = new mysqli("localhost", "root", "", "latihan_oop");
        if ($this->conn->connect_error) {
            die("Koneksi database gagal");
        }
    }

    public function query($sql) {
        return $this->conn->query($sql);
    }
}
```

---

## Langkah 8 – Halaman Login

File: `module/user/login.php`

```php
<?php
require_once "class/Database.php";

if ($_POST) {
    $db = new Database();

    $username = $_POST['username'];
    $password = $_POST['password'];

    $sql = "SELECT * FROM users WHERE username='$username' AND password='$password'";
    $result = $db->query($sql);

    if ($result->num_rows > 0) {
        $_SESSION['is_login'] = true;
        header("Location: index.php");
        exit;
    } else {
        $error = "Login gagal";
    }
}
?>

<h1>LOGIN</h1>

<?php if (!empty($error)) echo "<p style='color:red'>$error</p>"; ?>

<form method="post">
    <input name="username" placeholder="username"><br><br>
    <input type="password" name="password" placeholder="password"><br><br>
    <button>Login</button>
</form>
```

---

## Langkah 9 – Logout

File: `module/user/logout.php`

```php
<?php
session_destroy();
header("Location: index.php");
exit;
```

---

## Langkah 10 – Pengujian

1. Buka `http://localhost/lab11_php_oop/index.php`
2. Klik **Login**
3. Masukkan:

   * Username: `admin`
   * Password: `admin`
4. Login berhasil → kembali ke Home
5. Klik **Logout** → kembali ke Home

---

## Hasil Akhir

* Login berhasil menggunakan database
* Session aktif setelah login
* Logout menghapus session
* Menu berubah sesuai status login

---

## Kesimpulan

Pada praktikum ini berhasil dibuat sistem autentikasi sederhana menggunakan PHP dan MySQL dengan memanfaatkan session. Sistem ini dapat dikembangkan lebih lanjut dengan enkripsi password dan proteksi halaman.

---

## Catatan Pengembangan

* Menggunakan `password_hash()` dan `password_verify()`
* Menambahkan halaman profil user
* Menambahkan proteksi halaman CRUD
