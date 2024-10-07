# prak_modul4

Nabila Winanda Meirani - H1D022108

## Demo

![Demo Video](2024-10-07%2016-18-22.gif)


## Proses Registrasi 
1. Menuju halaman registrasi<br>
Ketika pertama kali membuka aplikasi maka akan langsung ditampilkan halaman login, dari halaman login ini kita menekan tombol registrasi untuk masuk ke halaman registrasi dan melakukan registrasi.
![Screenshot 2024-10-07 162135](https://github.com/user-attachments/assets/2a918426-3b06-406a-a2e0-7e6b55698bb9)

3. Mengisi form registrasi
form ini memerlukan inputan nama, email, password, dan konfirm password
![Screenshot 2024-10-07 162222](https://github.com/user-attachments/assets/7a6ed6e9-3d1f-4e31-b334-6840a5ac29fa)

4. Pengiriman registrasi atau pendaftaran user
Setelah mengisi form registrasi, maka klik tombol registrasi dan alert akan muncul jika berhasil maupun gagal.
![Screenshot 2024-10-07 162311](https://github.com/user-attachments/assets/b4041116-acc2-4d6e-a676-87bc4c23f997)

```
  void _submit() {
    _formKey.currentState!.save();
    setState(() {
      _isLoading = true;
    });
    RegistrasiBloc.registrasi(
            nama: _namaTextboxController.text,
            email: _emailTextboxController.text,
            password: _passwordTextboxController.text)
        .then((value) {
      showDialog(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) => SuccessDialog(
                description: "Registrasi berhasil, silahkan login",
                okClick: () {
                  Navigator.pop(context);
                },
              ));
    }, onError: (error) {
      showDialog(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) => const WarningDialog(
                description: "Registrasi gagal, silahkan coba lagi",
              ));
    });
    setState(() {
      _isLoading = false;
    });
  }
}
```

kode diatas merupakan sebuah fungsi _submit yang dijalankan ketika tombol registrasi ditekan. Pertama tama, form validasi disimpan menggunakan _formKey.currentState!.save(), lalu status loading diubah menjadi true menggunakan setState(). Fungsi RegistrasiBloc.registrasi() dipanggil dan mengirimkan nilai nama, email, dan password yang diambil dari TextEditingController. Jika registrasi berhasil, maka SuccessDialog akan ditampilkan dan diarahkan ke login. Jika registrasi gagal, maka WarningDialog ditampilkan. Setelah semua proses selesai, status loading diubah kembali menjadi false menggunakan setState().


## Proses Login
1. Menampilkan dan mengisi form halaman login
Halaman login ini akan ditampilkan ketika kita baru saja membuka aplikasi dan ketika kita telah berhasil registrasi. form di halaman login ini memerlukan inputan email dan password. 
![Screenshot 2024-10-07 162135](https://github.com/user-attachments/assets/d9d5c24b-1660-43cf-83ab-8a4849ab6254)


2. Hasil login
Jika email dan password yang kita input salah dan tidak terdaftar maka akan memunculkan pesan gagal. Namun jika email dan password yang diinputkan benar maka aplikasi akan langsung menampilkan halaman list produk. Di dalam sistem jika login berhasil dilakukan maka token dan UserID akan disimpan.
![Screenshot 2024-10-07 162335](https://github.com/user-attachments/assets/8ca1697e-b415-4379-be0d-c8cbb0c3dd80)
![Screenshot 2024-10-07 162405](https://github.com/user-attachments/assets/7d09a7d6-7d3b-4687-88b5-4f681c2be6d5)


```
  void _submit() {
    _formKey.currentState!.save();
    setState(() {
      _isLoading = true;
    });
    LoginBloc.login(
            email: _emailTextboxController.text,
            password: _passwordTextboxController.text)
        .then((value) async {
      if (value.code == 200) {
        await UserInfo().setToken(value.token.toString());
        await UserInfo().setUserID(int.parse(value.userID.toString()));
        Navigator.pushReplacement(context,
            MaterialPageRoute(builder: (context) => const ProdukPage()));
      } else {
        showDialog(
            context: context,
            barrierDismissible: false,
            builder: (BuildContext context) => const WarningDialog(
                  description: "Login gagal, silahkan coba lagi",
                ));
      }
    }, onError: (error) {
      print(error);
      showDialog(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) => const WarningDialog(
                description: "Login gagal, silahkan coba lagi",
              ));
    });
    setState(() {
      _isLoading = false;
    });
  }
```

Kode di atas adalah sebuah fungsi _submit() yang dijalankan ketika tombol login ditekan. Pertama, form disimpan dengan _formKey.currentState!.save(), dan status loading diubah menjadi true pakai setState(). Fungsi LoginBloc.login() lalu dipanggil sambil mengirimkan nilai email dan password dari TextEditingController. Jika login berhasil (kode respons 200), token dan userID disimpan menggunakan metode setToken() dan setUserID() dari UserInfo(), lalu pengguna diarahkan ke halaman list produk dengan Navigator.pushReplacement(). Jika login gagal, maka akan mumcul WarningDialog yang memberi tahu kalau login gagal. Setelah proses ini selesai, status loading diubah lagi menjadi false dengan setState().


## Menampilkan List Produk
Setelah login berhasil, maka akan ditampilkan Halaman list produk. Halaman ini menampilkan daftar-daftar produk yang tersedia di dalam data base, tiap card dari produk juga dapat di klik untuk menampilkan detail produknya.
![Screenshot 2024-10-07 162405](https://github.com/user-attachments/assets/5c2410f2-0907-4ce7-8ae4-bccda8c4c7e7)

```
class ItemProduk extends StatelessWidget {
  final Produk produk;
  const ItemProduk({Key? key, required this.produk}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return GestureDetector(
      onTap: () {
        Navigator.push(
            context,
            MaterialPageRoute(
                builder: (context) => ProdukDetail(
                      produk: produk,
                    )));
      },
      child: Card(
        child: ListTile(
          title: Text(produk.namaProduk!),
          subtitle: Text(produk.hargaProduk.toString()),
        ),
      ),
    );
  }
}
```

Kode di atas merupakan widget stateless ItemProduk yang menerima parameter Produk. Widget ini menampilkan produk dalam bentuk Card yang berisi nama produk dan harga menggunakan ListTile. Ketika pengguna menekan card produknya, widget mengarahkan ke halaman detail produk menggunakan Navigator.push(), sekaligus mengirimkan data produk yang dipilih ke halaman tersebut. Fungsi ini digunakan untuk menampilkan daftar produk dan mengarahkan pengguna ke tampilan detail produk saat item ditekan.


## Menampilkan Detail Produk
Detail produk adalah halaman yang muncuk ketika card produk di halaman list produk ditekan. Detail produk ini menampilkan kode, nama, dan harga produk. selain itu di halaman detail produk ini terdapat tombol untuk ubah atau edit dan tombol untuk menghapus produk atau delete.
![Screenshot 2024-10-07 162955](https://github.com/user-attachments/assets/4b7c5545-8dcf-4076-b4b2-e7098c74000a)

```
class ProdukDetail extends StatefulWidget {
  Produk? produk;
  ProdukDetail({Key? key, this.produk}) : super(key: key);
  @override
  _ProdukDetailState createState() => _ProdukDetailState();
}

class _ProdukDetailState extends State<ProdukDetail> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Detail Produk'),
      ),
      body: Center(
        child: Column(
          children: [
            Text(
              "Kode : ${widget.produk!.kodeProduk}",
              style: const TextStyle(fontSize: 20.0),
            ),
            Text(
              "Nama : ${widget.produk!.namaProduk}",
              style: const TextStyle(fontSize: 18.0),
            ),
            Text(
              "Harga : Rp. ${widget.produk!.hargaProduk.toString()}",
              style: const TextStyle(fontSize: 18.0),
            ),
            _tombolHapusEdit()
          ],
        ),
      ),
    );
  }
```

Kode di atas adalah widget bernama ProdukDetail yang merupakan subclass dari StatefulWidget. Widget ini menerima parameter produk, yang merupakan objek dari class Produk. createState() digunakan untuk membuat dan menghubungkan state dan widget ini melalui _ProdukDetailState. Dalam class _ProdukDetailState,  build() menghasilkan tampilan halaman detail produk. Di halaman ini terdapat AppBar atau judul "Detail Produk" dan menampilkan informasi produk. Informasi produk diambil dari objek produk menggunakan widget.produk. Selain itu, ada pemanggilan fungsi _tombolHapusEdit(), yang bertujuan untuk menampilkan tombol delete dan ubah.












