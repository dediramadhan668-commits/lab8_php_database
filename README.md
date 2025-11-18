# LAPORAN TUGAS PRAKTIKUM WEB PROGRAMMING 💻
# Lab 8: Implementasi CRUD Sederhana dengan PHP dan MySQLi
Keterangan	Detail
# Nama	Dedi Ramadhan
# NIM	312410171
# Kelas	TI 24 A 1
# Mata Kuliah	Web Programming

1. Deskripsi Proyek 📄
Proyek ini adalah implementasi CRUD (Create, Read, Update, Delete) sederhana untuk mengelola data barang. Aplikasi ini dikembangkan menggunakan bahasa pemrograman PHP dengan ekstensi MySQLi untuk interaksi database. Fitur utama mencakup tampilan data, penambahan data baru (termasuk unggah gambar), pengubahan data, dan penghapusan data.

2. Struktur Database 💾
Database yang digunakan bernama latihan1. Di dalamnya terdapat satu tabel utama, yaitu data_barang.

SQL Script
```SQL
CREATE DATABASE latihan1;

USE latihan1;

CREATE TABLE data_barang (
    id_barang INT(11) NOT NULL AUTO_INCREMENT,
    nama VARCHAR(30) NOT NULL,
    kategori VARCHAR(30) NOT NULL,
    harga_jual INT(11) NOT NULL,
    harga_beli INT(11) NOT NULL,
    stok INT(11) NOT NULL,
    gambar VARCHAR(100),
    PRIMARY KEY (id_barang)
);
```

3. Dokumentasi Sesi (Screenshoot) 📸
Berikut adalah tangkapan layar (screenshot) hasil eksekusi program yang menunjukkan fungsionalitas aplikasi:


<img width="826" height="329" alt="image" src="https://github.com/user-attachments/assets/d9faf337-e8aa-4ae7-8c9c-6aa4777f13b6" />


<img width="650" height="266" alt="image" src="https://github.com/user-attachments/assets/4dd06f7c-1529-498e-839c-b4b2e07fe6cc" />

4. Source Code 💻
Berikut adalah detail kode sumber untuk setiap file PHP dalam proyek ini:

A. koneksi.php (Koneksi Database)
Menetapkan parameter koneksi dan membuat objek koneksi MySQLi.

```PHP
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "latihan1";

$conn = mysqli_connect($host, $user, $pass, $db);
if (!$conn) {
    die("Koneksi gagal");
}
?>
```
B. index.php (Read & Tampilan Data)
Menampilkan seluruh data dari tabel data_barang dalam bentuk tabel HTML.

```PHP
<?php
include("koneksi.php");

// Query untuk menampilkan data
$sql = "SELECT * FROM data_barang";
$result = mysqli_query($conn, $sql);
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Data Barang</title>
</head>
<body>
    <h1>Data Barang</h1>
    <a href="tambah.php">Tambah Data</a>
    <table border="1" cellpadding="6" width="100%">
        <thead>
            <tr>
                <th>Gambar</th>
                <th>Nama</th>
                <th>Kategori</th>
                <th>Harga Jual</th>
                <th>Harga Beli</th>
                <th>Stok</th>
                <th>Aksi</th>
            </tr>
        </thead>
        <tbody>
            <?php while($row = mysqli_fetch_assoc($result)): ?>
            <tr>
                <td><img src="gambar/<?= $row['gambar'];?>" width="80"></td>
                <td><?= $row['nama'];?></td>
                <td><?= $row['kategori'];?></td>
                <td><?= $row['harga_jual'];?></td>
                <td><?= $row['harga_beli'];?></td>
                <td><?= $row['stok'];?></td>
                <td>
                    <a href="ubah.php?id=<?= $row['id_barang'];?>">Ubah</a> | 
                    <a href="hapus.php?id=<?= $row['id_barang'];?>">Hapus</a>
                </td>
            </tr>
            <?php endwhile; ?>
        </tbody>
    </table>
</body>
</html>
```
C. tambah.php (Create)
Menangani input data baru, proses unggah gambar, dan menyimpan ke database.

```PHP
<?php
include 'koneksi.php';

if (isset($_POST['submit'])) {
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $gambar = "";

    // Proses upload file
    if ($_FILES['file_gambar']['error'] == 0) {
        $fn = str_replace(" ", "_", $_FILES['file_gambar']['name']);
        move_uploaded_file($_FILES['file_gambar']['tmp_name'], "gambar/" . $fn);
        $gambar = $fn;
    }

    $sql = "INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) 
            VALUES ('$nama', '$kategori', '$harga_jual', '$harga_beli', '$stok', '$gambar')";
    
    mysqli_query($conn, $sql);
    header("Location: index.php");
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Tambah Barang</title>
</head>
<body>
    <h1>Tambah Barang</h1>
    <form method="post" enctype="multipart/form-data">
        <label>Nama:</label>
        <input type="text" name="nama"><br>
        
        <label>Kategori:</label>
        <input type="text" name="kategori"><br>
        
        <label>Harga Jual:</label>
        <input type="text" name="harga_jual"><br>
        
        <label>Harga Beli:</label>
        <input type="text" name="harga_beli"><br>
        
        <label>Stok:</label>
        <input type="text" name="stok"><br>
        
        <label>Gambar:</label>
        <input type="file" name="file_gambar"><br>
        
        <button type="submit" name="submit">Simpan</button>
    </form>
</body>
</html>
```
D. ubah.php (Update)
Mengambil data berdasarkan ID, menampilkan data di form, dan memperbarui data di database (termasuk mengganti gambar).

```PHP
<?php
include 'koneksi.php';

$id = $_GET['id'];
$q = mysqli_query($conn, "SELECT * FROM data_barang WHERE id_barang = $id");
$d = mysqli_fetch_assoc($q);

if (isset($_POST['submit'])) {
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $gambar = $d['gambar']; // Default ke gambar lama

    // Jika ada file baru yang diupload
    if ($_FILES['file_gambar']['error'] == 0) {
        $fn = str_replace(" ", "_", $_FILES['file_gambar']['name']);
        move_uploaded_file($_FILES['file_gambar']['tmp_name'], "gambar/" . $fn);
        $gambar = $fn;
    }

    $sql = "UPDATE data_barang SET 
            nama = '$nama', 
            kategori = '$kategori', 
            harga_jual = '$harga_jual', 
            harga_beli = '$harga_beli', 
            stok = '$stok', 
            gambar = '$gambar' 
            WHERE id_barang = $id";

    mysqli_query($conn, $sql);
    header("Location: index.php");
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Ubah Barang</title>
</head>
<body>
    <h1>Ubah Barang</h1>
    <form method="post" enctype="multipart/form-data">
        <label>Nama:</label>
        <input type="text" name="nama" value="<?= $d['nama']; ?>"><br>
        
        <label>Kategori:</label>
        <input type="text" name="kategori" value="<?= $d['kategori']; ?>"><br>
        
        <label>Harga Jual:</label>
        <input type="text" name="harga_jual" value="<?= $d['harga_jual']; ?>"><br>
        
        <label>Harga Beli:</label>
        <input type="text" name="harga_beli" value="<?= $d['harga_beli']; ?>"><br>
        
        <label>Stok:</label>
        <input type="text" name="stok" value="<?= $d['stok']; ?>"><br>
        
        <label>Gambar Saat Ini:</label>
        <img src="gambar/<?= $d['gambar']; ?>" width="80"><br>
        <label>Ganti Gambar:</label>
        <input type="file" name="file_gambar"><br>
        
        <button type="submit" name="submit">Simpan Perubahan</button>
    </form>
</body>
</html>
E. hapus.php (Delete)
Menghapus data barang berdasarkan ID yang dikirim melalui URL.

PHP
<?php
include 'koneksi.php';

$id = $_GET['id'];
$sql = "DELETE FROM data_barang WHERE id_barang = '$id'";
mysqli_query($conn, $sql);

header("Location: index.php");
?>
```
5. Kesimpulan
Proyek ini berhasil mengimplementasikan fungsionalitas CRUD (Create, Read, Update, Delete) dasar untuk pengelolaan inventaris barang, lengkap dengan kemampuan file upload menggunakan PHP dan MySQLi. Aplikasi dapat digunakan untuk menambah, melihat, mengubah, dan menghapus data barang secara efisien.
