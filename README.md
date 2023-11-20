# Lab8_Web

|  |  |  |
|-----|------|-----|
|Nama|Muhammad Rizqi Maulana|
|NIM|312210360|
|Kelas|TI.22.A.4|
|Mata Kuliah|Pemograman Web|

## Langkah-langkah Praktikum

1. **Persiapan**
    - Untuk memulai membuat aplikasi CRUD sederhana, yang perlu disiapkan adalah database server menggunakan MySQL. Pastikan MySQL Server sudah dapat dijalankan melalui XAMPP.

2. **Membuat Database**
    ```sql
    CREATE DATABASE latihan1;
    ```

3. **Membuat Tabel**
    ```sql
    CREATE TABLE data_barang (
        id_barang int(10) auto_increment Primary Key,
        kategori varchar(30),
        nama varchar(30),
        gambar varchar(100),
        harga_beli decimal(10,0),
        harga_jual decimal(10,0),
        stok int(4)
    );
    ```
    ![img](gambar/1.png)<br>

4. **Menambahkan Data**
    ```sql
    INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
    VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
    ('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
    ('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
    ```
    ![img](gambar/2.png)<br>

5. **Membuat Program CRUD**
    - Buat folder **lab8_php_database** pada root directory web server (**d:\xampp\htdocs**)<br>
    - Kemudian untuk mengakses direktory tersebut pada web server dengan mengakses URL: http://localhost/lab8_php_database/<br>
    ![img](gambar/3.png)<br>

6. **Membuat file koneksi Database & Buat file koneksi.php**
    ```php
    <?php
    $host = "localhost";
    $user = "root";
    $pass = "";
    $db = "latihan1";

    $conn = mysqli_connect($host, $user, $pass, $db);
    if ($conn == false)
    {
        echo "Koneksi ke server gagal.";
        die();
    } #else echo "Koneksi berhasil";
    ?>
    ```
    ![img](gambar/4.png)<br>

7. **Membuat file index.php untuk menampilkan data (READ)**

    ```php
    <?php
    include("koneksi.php");

    // query untuk menampilkan data
    $sql = 'SELECT * FROM data_barang';
    $result = mysqli_query($conn, $sql);
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <link href="style.css" rel="stylesheet" type="text/css" />
        <style>
            /* Style CSS yang dibutuhkan */
    body {
        font-family: Arial, sans-serif;
        background-color: #f4f4f4;
        margin: 0;
        padding: 0;
    }

    .container {
        max-width: 800px;
        margin: 20px auto;
        background-color: #fff;
        padding: 20px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }

    h1 {
        text-align: center;
    }

    .main {
        margin-top: 20px;
    }

    table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
    }

    th, td {
        border: 1px solid #ddd;
        padding: 12px;
        text-align: left;
    }

    th {
        background-color: #f2f2f2;
    }

    img {
        max-width: 80px;
        height: auto;
    }

    .aksi {
        display: flex;
        justify-content: space-around;
    }

    .tambah {
        display: inline-block;
        padding: 10px 15px;
        background-color: #007bff;
        color: #fff;
        text-decoration: none;
        border-radius: 5px;
    }

        </style>
        <title>Data Barang</title>
    </head>
    <body>
        <div class="container">
            <h1>Data Barang</h1>
            <div class="main">
                <a class="tambah" href="tambah.php">Tambah Barang</a>
                <table>
                    <tr>
                        <th>Gambar</th>
                        <th>Nama Barang</th>
                        <th>Kategori</th>
                        <th>Harga Jual</th>
                        <th>Harga Beli</th>
                        <th>Stok</th>
                        <th>Aksi</th>
                    </tr>
                    <?php if($result): ?>
                        <?php while($row = mysqli_fetch_array($result)): ?>
                            <tr>
                                <td><img src="gambar/<?= $row['gambar'];?>" alt="<?= $row['nama'];?>"></td>
                                <td><?= $row['nama'];?></td>
                                <td><?= $row['kategori'];?></td>
                                <td><?= $row['harga_beli'];?></td>
                                <td><?= $row['harga_jual'];?></td>
                                <td><?= $row['stok'];?></td>
                                <td class="aksi">
                                    <a class="ubah" href="ubah.php?id=<?= $row['id_barang']; ?>">Ubah</a>
                                    <a class="hapus" href="hapus.php?id=<?= $row['id_barang']; ?>">Hapus</a>
                                </td>
                            </tr>
                        <?php endwhile; else: ?>
                            <tr>
                                <td colspan="7">Belum ada data</td>
                            </tr>
                        <?php endif; ?>
                </table>
            </div>
        </div>
    </body>
    </html>
    ```
    ![img](gambar/5.png)<br>

8. **Menambahkan Data (CREATE) & Buat file baru tambah.php**
    ```php
    <?php
    error_reporting(E_ALL);
    include_once 'koneksi.php';

    if (isset($_POST['submit'])) {
        $nama = $_POST['nama'];
        $kategori = $_POST['kategori'];
        $harga_jual = $_POST['harga_jual'];
        $harga_beli = $_POST['harga_beli'];
        $stok = $_POST['stok'];
        $file_gambar = $_FILES['file_gambar'];
        $gambar = null;

        if ($file_gambar['error'] == 0) {
            $filename = str_replace(' ', '_', $file_gambar['name']);
            $destination = dirname(__FILE__) . '/gambar/' . $filename;
            
            if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
                $gambar = 'gambar/' . $filename;
            }
        }

        $sql = "INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar) ";
        $sql .= "VALUES ('{$nama}', '{$kategori}', '{$harga_jual}', '{$harga_beli}', '{$stok}', '{$gambar}')";

        $result = mysqli_query($conn, $sql);

        if (!$result) {
            echo "Error: " . mysqli_error($conn);
        } else {
            header('location: index.php');
        }
    }
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <link href="style.css" rel="stylesheet" type="text/css" />
        <title>Tambah Barang</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                background-color: #f4f4f4;
                margin: 0;
                padding: 0;
            }

            .container {
                max-width: 600px;
                margin: 20px auto;
                background-color: #fff;
                padding: 20px;
                box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            }

            h1 {
                text-align: center;
            }

            .main {
                margin-top: 20px;
            }

            form {
                width: 100%;
                max-width: 400px;
                margin: 0 auto;
            }

            .input {
                margin-bottom: 15px;
            }

            label {
                display: block;
                margin-bottom: 5px;
            }

            input, select {
                width: 100%;
                padding: 8px;
                box-sizing: border-box;
            }

            .submit {
                text-align: center;
                margin-top: 15px;
            }

            input[type="submit"] {
                background-color: #007bff;
                color: #fff;
                border: none;
                padding: 10px 15px;
                border-radius: 5px;
                cursor: pointer;
            }
        </style>
    </head>
        <div class="container">
            <h1>Tambah Barang</h1>
            <div class="main">
                <form method="post" action="tambah.php" enctype="multipart/form-data">
                    <div class="input">
                        <label>Nama Barang</label>
                        <input type="text" name="nama" required />
                    </div>
                    <div class="input">
                        <label>Kategori</label>
                        <select name="kategori" required>
                            <option value="Komputer">Komputer</option>
                            <option value="Elektronik">Elektronik</option>
                            <option value="Hand Phone">Hand Phone</option>
                        </select>
                    </div>
                    <div class="input">
                        <label>Harga Jual</label>
                        <input type="text" name="harga_jual" required />
                    </div>
                    <div class="input">
                        <label>Harga Beli</label>
                        <input type="text" name="harga_beli" required />
                    </div>
                    <div class="input">
                        <label>Stok</label>
                        <input type="text" name="stok" required />
                    </div>
                    <div class="input">
                        <label>File Gambar</label>
                        <input type="file" name="file_gambar" required />
                    </div>
                    <div class="submit">
                        <input type="submit" name="submit" value="Simpan" />
                    </div>
                </form>
            </div>
        </div>
    </body>
    </html>
    ```
    ![img](gambar/6.png)<br>

9. **Mengubah Data (UPDATE) & buat file baru ubah.php**

    ```php
    <?php
    error_reporting(E_ALL);
    include_once 'koneksi.php';

    if (isset($_POST['submit'])) {
        $id = $_POST['id'];
        $nama = $_POST['nama'];
        $kategori = $_POST['kategori'];
        $harga_jual = $_POST['harga_jual'];
        $harga_beli = $_POST['harga_beli'];
        $stok = $_POST['stok'];
        $file_gambar = $_FILES['file_gambar'];
        $gambar = null;

        if ($file_gambar['error'] == 0) {
            $filename = str_replace(' ', '_', $file_gambar['name']);
            $destination = dirname(__FILE__) . '/gambar/' . $filename;
            if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
                $gambar = 'gambar/' . $filename;
            }
        }

        $sql = 'UPDATE data_barang SET ';
        $sql .= "nama = '{$nama}', kategori = '{$kategori}', ";
        $sql .= "harga_jual = '{$harga_jual}', harga_beli = '{$harga_beli}', stok = '{$stok}' ";
        if (!empty($gambar)) {
            $sql .= ", gambar = '{$gambar}' ";
        }
        $sql .= "WHERE id_barang = '{$id}'";
        $result = mysqli_query($conn, $sql);

        if (!$result) {
            die('Error: ' . mysqli_error($conn));
        } else {
            header('location: index.php');
        }
    }

    $id = $_GET['id'];
    $sql = "SELECT * FROM data_barang WHERE id_barang = '{$id}'";
    $result = mysqli_query($conn, $sql);
    if (!$result) {
        die('Error: Data tidak tersedia');
    }
    $data = mysqli_fetch_array($result);

    function is_select($var, $val) {
        if ($var == $val) return 'selected="selected"';
        return false;
    }
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <link href="style.css" rel="stylesheet" type="text/css" />
        <title>Ubah Barang</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                background-color: #f4f4f4;
                margin: 0;
                padding: 0;
            }

            .container {
                max-width: 500px;
                margin: 20px auto;
                background-color: #fff;
                padding: 20px;
                box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
                border-radius: 5px;
            }

            h1 {
                text-align: center;
                color: #007bff;
            }

            .main {
                margin-top: 20px;
            }

            .input {
                margin-bottom: 15px;
            }

            label {
                display: block;
                margin-bottom: 5px;
                color: #333;
            }

            input, select {
                width: 100%;
                padding: 8px;
                box-sizing: border-box;
                border: 1px solid #ccc;
                border-radius: 4px;
            }

            .submit {
                text-align: center;
            }

            input[type="submit"] {
                background-color: #007bff;
                color: #fff;
                padding: 10px 15px;
                border: none;
                border-radius: 5px;
                cursor: pointer;
            }

            input[type="file"] {
                padding: 10px 0;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <h1>Ubah Barang</h1>
            <div class="main">
                <form method="post" action="ubah.php" enctype="multipart/form-data">
                    <div class="input">
                        <label>Nama Barang</label>
                        <input type="text" name="nama" value="<?php echo $data['nama'];?>" />
                    </div>
                    <div class="input">
                        <label>Kategori</label>
                        <select name="kategori">
                            <option <?php echo is_select('Komputer', $data['kategori']);?> value="Komputer">Komputer</option>
                            <option <?php echo is_select('Elektronik', $data['kategori']);?> value="Elektronik">Elektronik</option>
                            <option <?php echo is_select('Hand Phone', $data['kategori']);?> value="Hand Phone">Hand Phone</option>
                        </select>
                    </div>
                    <div class="input">
                        <label>Harga Jual</label>
                        <input type="text" name="harga_jual" value="<?php echo $data['harga_jual'];?>" />
                    </div>
                    <div class="input">
                        <label>Harga Beli</label>
                        <input type="text" name="harga_beli" value="<?php echo $data['harga_beli'];?>" />
                    </div>
                    <div class="input">
                        <label>Stok</label>
                        <input type="text" name="stok" value="<?php echo $data['stok'];?>" />
                    </div>
                    <div class="input">
                        <label>File Gambar</label>
                        <input type="file" name="file_gambar" />
                    </div>
                    <div class="submit">
                        <input type="hidden" name="id" value="<?php echo $data['id_barang'];?>" />
                        <input type="submit" name="submit" value="Simpan" />
                    </div>
                </form>
            </div>
        </div>
    </body>
    </html>
    ```
    ![img](gambar/7.png)

9. **Menghapus Data (DELETE) & Buat file baru hapus.php**

```php
<?php
include_once 'koneksi.php';
$id = $_GET['id'];
$sql = "DELETE FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
header('location: index.php');
?>
```

## HASIL

![gif](gambar/hasil.gif)