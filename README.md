# Tugas-Lanjutan-java

<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Aplikasi Buku Favorit</title>
    <link rel="stylesheet" href="style.css" />
</head>
<body>
    <h1>Aplikasi Buku Favorit</h1>

    <!-- Input Nama Pengguna -->
    <div id="session-container">
        <input type="text" id="namaPengguna" placeholder="Masukkan Nama Anda" />
        <button id="btnSimpanNama">Simpan Nama</button>
        <h3 id="salamPengguna"></h3>
    </div>

    <!-- Form Tambah Buku -->
    <form id="form-tambah-buku">
        <input type="text" id="judul" placeholder="Judul Buku" required />
        <input type="text" id="penulis" placeholder="Penulis" required />
        <input type="number" id="tahun" placeholder="Tahun Terbit" required />
        <button type="submit">Tambah Buku</button>
    </form>

    <!-- Daftar Buku -->
    <h2>Daftar Buku</h2>
    <div id="daftar-buku"></div>

    <!-- Buku Favorit -->
    <h2>Buku Favorit</h2>
    <div id="buku-favorit"></div>

    <script src="project.js"></script>
</body>
</html>

// Definisi Class Buku
class Buku {
    constructor(judul, penulis, tahun) {
        this.judul = judul;
        this.penulis = penulis;
        this.tahun = tahun;
    }

    tampilkanInfo() {
        return `${this.judul} oleh ${this.penulis} (${this.tahun})`;
    }
}

// Array untuk menyimpan daftar buku dan buku favorit
let daftarBuku = [];
let bukuFavorit = [];

// Mengambil elemen DOM
const formTambahBuku = document.getElementById('form-tambah-buku');
const divDaftarBuku = document.getElementById('daftar-buku');
const divBukuFavorit = document.getElementById('buku-favorit');
const btnSimpanNama = document.getElementById('btnSimpanNama');
const salamPengguna = document.getElementById('salamPengguna');

// Event Listener untuk Form Tambah Buku
formTambahBuku.addEventListener('submit', function (e) {
    e.preventDefault();
    tambahBuku();
});

// Fungsi untuk menambahkan buku ke daftar
function tambahBuku() {
    const judul = document.getElementById('judul').value;
    const penulis = document.getElementById('penulis').value;
    const tahun = document.getElementById('tahun').value;

    // Validasi input
    if (judul === '' || penulis === '' || tahun === '') {
        alert('Semua kolom harus diisi!');
        return;
    }

    const bukuBaru = new Buku(judul, penulis, tahun);
    daftarBuku.push(bukuBaru);
    simpanDaftarBuku(); // Simpan daftar buku setelah menambahkan buku baru
    tampilkanDaftarBuku();
    formTambahBuku.reset();
}

// Fungsi untuk menyimpan daftar buku ke Local Storage
function simpanDaftarBuku() {
    localStorage.setItem('daftarBuku', JSON.stringify(daftarBuku));
}

// Fungsi untuk menampilkan daftar buku
function tampilkanDaftarBuku() {
    divDaftarBuku.innerHTML = '';

    daftarBuku.forEach((buku, index) => {
        const divBuku = document.createElement('div');
        divBuku.classList.add('buku');
        divBuku.innerHTML = `
<p>${buku.tampilkanInfo()}</p>
<button onclick="tambahKeFavorit(${index})">Tambah ke Favorit</button>
`;
        divDaftarBuku.appendChild(divBuku);
    });
}

// Fungsi untuk menambahkan buku ke favorit
function tambahKeFavorit(index) {
    const buku = daftarBuku[index];

    // Cek apakah buku sudah ada di favorit
    const sudahAda = bukuFavorit.some(favBuku => {
        return favBuku.judul === buku.judul &&
            favBuku.penulis === buku.penulis &&
            favBuku.tahun === buku.tahun;
    });

    if (sudahAda) {
        alert('Buku ini sudah ada di daftar favorit!');
        return;
    }

    bukuFavorit.push(buku);
    simpanBukuFavorit();
    tampilkanBukuFavorit();
}

// Fungsi untuk menyimpan buku favorit ke Local Storage
function simpanBukuFavorit() {
    localStorage.setItem('bukuFavorit', JSON.stringify(bukuFavorit));
}

// Fungsi untuk menampilkan buku favorit
function tampilkanBukuFavorit() {
    divBukuFavorit.innerHTML = '';

    bukuFavorit.forEach((buku, index) => {
        const divBuku = document.createElement('div');
        divBuku.classList.add('buku');
        divBuku.innerHTML = `
<p>${buku.tampilkanInfo()}</p>
<button onclick="hapusDariFavorit(${index})">Hapus</button>
`;
        divBukuFavorit.appendChild(divBuku);
    });
}

// Fungsi untuk menghapus buku dari favorit
function hapusDariFavorit(index) {
    bukuFavorit.splice(index, 1);
    simpanBukuFavorit();
    tampilkanBukuFavorit();
}

// Event Listener untuk tombol simpan nama pengguna
btnSimpanNama.addEventListener('click', function () {
    const nama = document.getElementById('namaPengguna').value;
    if (nama === '') {
        alert('Masukkan nama Anda!');
        return;
    }
    sessionStorage.setItem('namaPengguna', nama);
    tampilkanNamaPengguna();
    document.getElementById('namaPengguna').value = '';
});

// Fungsi untuk menampilkan nama pengguna
function tampilkanNamaPengguna() {
    const nama = sessionStorage.getItem('namaPengguna');

    if (nama) {
        salamPengguna.textContent = `Selamat datang, ${nama}!`;
    } else {
        salamPengguna.textContent = '';
    }
}

// Memuat data saat halaman dimuat
window.onload = function () {
    // Memuat daftar buku dari Local Storage
    if (localStorage.getItem('daftarBuku')) {
        const storedBooks = JSON.parse(localStorage.getItem('daftarBuku'));
        // Rekonstruksi objek buku menjadi instance dari kelas Buku
        daftarBuku = storedBooks.map(book => {
            return new Buku(book.judul, book.penulis, book.tahun);
        });
        tampilkanDaftarBuku();
    }

    // Memuat buku favorit dari Local Storage
    if (localStorage.getItem('bukuFavorit')) {
        const storedFavorites = JSON.parse(localStorage.getItem('bukuFavorit'));
        // Rekonstruksi objek buku menjadi instance dari kelas Buku
        bukuFavorit = storedFavorites.map(book => {
            return new Buku(book.judul, book.penulis, book.tahun);
        });
        tampilkanBukuFavorit();
    }

    // Menampilkan nama pengguna dari Session Storage
    tampilkanNamaPengguna();
};

body {
    font-family: Arial, sans-serif;
    margin: 20px;
}

h1, h2, h3 {
    color: #333;
}

#session-container {
    margin-bottom: 20px;
}

#session-container input {
    margin-right: 10px;
}

form, .buku {
    margin-bottom: 20px;
}

input {
    margin: 5px 0;
    padding: 8px;
    width: calc(100% - 20px);
    box-sizing: border-box;
}

button {
    padding: 8px 15px;
    background-color: #007BFF;
    color: #fff;
    border: none;
    cursor: pointer;
}

button:hover {
    background-color: #0056b3;
}

#daftar-buku, #buku-favorit {
    border: 1px solid #ccc;
    padding: 10px;
}

.buku {
    border-bottom: 1px solid #eee;
    padding: 10px 0;
}

.buku:last-child {
    border-bottom: none;
}

.buku p {
    margin: 0;
}

.buku button {
    margin-top: 10px;
}
