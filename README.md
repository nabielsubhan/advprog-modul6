# Module 6 Reflection Notes

**Nama      : Muhammad Nabiel Subhan**
**NPM       : 2206081553**
**Kelas     : AdPro-B**

## Commit 1 Reflection Notes

Function `handle_connection` berfungsi untuk mengelola koneksi TCP dengan client. Fungsi ini menerima parameter `stream` yang berupa `TcpStream`. Kemudian, koneksi TCP dibaca menggunakan `BufReader::new(&mut stream);` baris demi baris hingga bertemu dengan baris kosong yang menandakan bahwa seluruh informasi HTTP request telah terambil dan informasi-informasi tersebut akan disimpan di `http_request`. Setelah selesai membaca koneksi TCP, hasil dari proses pembacaan tersebut kemudian dicetak ke konsol dengan menggunakan perintah `println!`.

## Commit 2 Reflection Notes

Function `handle_connection` yang sebelumnya telah dimodifikasi sehingga yang sekarang memungkinkan untuk membuat response HTTP. Setelah membaca HTTP request seperti sebelumnya, function yang baru akan membuat respons HTTP lengkap dengan format yang benar, yaitu status_line, headers, dan body yang berisikan konten pada berkas `hello.html`. Respons HTTP tersebut kemudian dikirimkan ke client menggunakan koneksi TCP `stream` menggunakan `stream.write_all` dan konten dari `hello.html` akan ditampilkan ke client.
<p align="center">
  <img src="assets\images\commit2.png" />
</p>