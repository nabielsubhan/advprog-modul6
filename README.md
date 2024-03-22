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

## Commit 3 Reflection Notes

Sebelumnya, function `handle_connection` akan selalu mengirimkan response yang berisikan konten dari berkas `hello.html` apapun request yang diterima. Sekarang, kita akan menambahkan *functionality* untuk pengecekan request agar konten yang diberikan sesuai dengan request yang diterima. Function `handle_connection` diubah menjadi sebagai berikut.
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    } else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
}
```
Pada function terbaru tersebut, kita cukup melihat baris pertama dari HTTP request yang diterima dan melakukan pengecekan pada *conditional branch*. Jika `request_line` merupakan GET request, maka konten yang akan diberikan pada HTTP response adalah konten pada `hello.html`, sedangkan jika `request_line` selain dari GET request, maka konten yang diberikan adalah konten pada `404.html`. Sebagai contoh, jika kita mencoba mengakses `127.0.0.1:7878/bad`, maka konten `404.html` akan muncul.
<p align="center">
  <img src="assets\images\commit3.png" />
</p>

Namun, jika kita lihat, kode pada *block if* dan *else* memiliki pengulangan penulisan kode sehingga menjadi kurang efisien. Oleh karena itu, kita perlu melakukan *refactoring* pada kode agar lebih efisien dan memenuhi prinsip *clean code* DRY (*Don't Repeat Yourself*). Ubah function `handle_connection` menjadi sebagai berikut.
```rust
fn handle_connection(mut stream: TcpStream) {
    // --snip--

    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };

    let contents = fs::read_to_string(filename).unwrap();
    let length = contents.len();

    let response =
        format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
}
```
Dengan begitu, kita sudah mengubahnya dengan menghilangkan duplikasi kode tanpa mengubah *functionality* asli dari function tersebut.