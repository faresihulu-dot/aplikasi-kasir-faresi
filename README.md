# aplikasi-kasir-faresi
Aplikasi Kasir Faresi
<!doctype html><html lang="id" xmlns:th="http://www.thymeleaf.org"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Login - Kasir Faresi</title><link rel="stylesheet" th:href="@{/css/style.css}"></head><body class="login-bg"><main class="login-card"><div class="logo">KF</div><h1>Aplikasi Kasir Faresi</h1><p class="muted">Silakan masuk untuk melanjutkan</p><div class="alert" th:if="${param.error}">Username atau password salah.</div><div class="success" th:if="${param.logout}">Kamu berhasil keluar.</div><form th:action="@{/login}" method="post"><label>Username</label><input name="username" autocomplete="username" required autofocus><label>Password</label><input type="password" name="password" autocomplete="current-password" required><button class="btn primary full" type="submit">Masuk</button></form><p class="muted small">Aplikasi Kasir Faresi · Sistem Penjualan</p></main></body></html>

<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Aplikasi Kasir Faresi</title>
<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial;background:#edf2f8;color:#17243a}
header{background:#123d70;color:white;padding:22px;text-align:center}
main{max-width:850px;margin:25px auto;padding:15px}
.card{background:white;padding:22px;border-radius:13px;margin-bottom:20px;box-shadow:0 4px 15px #0001}
h2{color:#174e86}
input,select{width:100%;padding:12px;margin:7px 0 15px;border:1px solid #ccd5df;border-radius:7px}
button{padding:12px 18px;border:0;border-radius:7px;background:#1767b1;color:white;font-weight:bold;cursor:pointer;margin:4px}
button:hover{opacity:.85}
.danger{background:#c0392b}
.green{background:#16834c}
.hidden{display:none}
table{width:100%;border-collapse:collapse}
td,th{padding:10px;border-bottom:1px solid #ddd;text-align:left}
.receipt{border:1px dashed #777;padding:15px;margin-top:15px}
#login{max-width:380px;margin:70px auto}
.center{text-align:center}
@media print{header,.no-print,.card:first-child{display:none!important}body{background:white}.card{box-shadow:none}}
</style>
</head>
<body>

<header>
<h1>APLIKASI KASIR FARESI</h1>
<p>Sistem Kasir Penjualan Barang</p>
</header>

<main>

<div id="login" class="card">
<h2 class="center">LOGIN KASIR</h2>
<label>Username</label>
<input id="username" placeholder="Username">
<label>Password</label>
<input id="password" type="password" placeholder="Password">
<button style="width:100%" onclick="login()">MASUK</button>
<p id="pesan" style="color:red"></p>
<p class="center">Admin: admin / admin123</p>
<p class="center">Kasir: kasir / kasir123</p>
</div>

<div id="aplikasi" class="hidden">

<div class="card no-print">
<h2>Dashboard</h2>
<p>Selamat datang, <b id="namaUser"></b></p>
<button class="danger" onclick="logout()">KELUAR</button>
</div>

<div class="card no-print" id="formProduk">
<h2>Tambah Barang</h2>
<input id="namaBarang" placeholder="Nama barang">
<input id="hargaBarang" type="number" min="0" placeholder="Harga barang">
<input id="stokBarang" type="number" min="0" placeholder="Stok barang">
<button onclick="tambahProduk()">SIMPAN BARANG</button>
</div>

<div class="card no-print">
<h2>Daftar Barang</h2>
<div style="overflow-x:auto">
<table>
<thead><tr><th>Nama</th><th>Harga</th><th>Stok</th><th>Aksi</th></tr></thead>
<tbody id="daftarBarang"></tbody>
</table>
</div>
</div>

<div class="card no-print">
<h2>Transaksi Penjualan</h2>
<label>Pilih Barang</label>
<select id="pilihBarang" onchange="hitungTotal()">
<option value="">Pilih barang</option>
</select>
<label>Jumlah</label>
<input id="jumlah" type="number" min="1" value="1" oninput="hitungTotal()">
<p>Total: <b id="total">Rp0</b></p>
<label>Uang Pembayaran</label>
<input id="bayar" type="number" min="0" placeholder="Masukkan uang" oninput="hitungTotal()">
<button class="green" onclick="prosesTransaksi()">PROSES PEMBAYARAN</button>
<p id="error" style="color:red"></p>
</div>

<div class="card">
<h2>Struk Pembayaran</h2>
<div id="struk">Belum ada transaksi.</div>
<button class="no-print" onclick="window.print()">CETAK STRUK</button>
</div>

<div class="card">
<h2>Riwayat Transaksi</h2>
<div style="overflow-x:auto">
<table>
<thead><tr><th>Barang</th><th>Jumlah</th><th>Total</th><th>Kembalian</th></tr></thead>
<tbody id="riwayat"></tbody>
</table>
</div>
</div>

</div>
</main>

<script>
let produk=JSON.parse(localStorage.getItem("produkFaresi")||"[]");
let riwayat=JSON.parse(localStorage.getItem("riwayatFaresi")||"[]");
let role="";

function login(){
 let u=document.getElementById("username").value;
 let p=document.getElementById("password").value;

 if(u==="admin"&&p==="admin123") role="admin";
 else if(u==="kasir"&&p==="kasir123") role="kasir";
 else{
  document.getElementById("pesan").innerText="Username atau password salah!";
  return;
 }

 document.getElementById("login").classList.add("hidden");
 document.getElementById("aplikasi").classList.remove("hidden");
 document.getElementById("namaUser").innerText=role;
 if(role==="kasir")document.getElementById("formProduk").classList.add("hidden");
 tampilProduk();
 tampilRiwayat();
}

function logout(){
 role="";
 document.getElementById("login").classList.remove("hidden");
 document.getElementById("aplikasi").classList.add("hidden");
 document.getElementById("username").value="";
 document.getElementById("password").value="";
}

function simpan(){
 localStorage.setItem("produkFaresi",JSON.stringify(produk));
 localStorage.setItem("riwayatFaresi",JSON.stringify(riwayat));
}

function rupiah(n){
 return "Rp"+Number(n).toLocaleString("id-ID");
}

function tambahProduk(){
 let nama=document.getElementById("namaBarang").value.trim();
 let harga=Number(document.getElementById("hargaBarang").value);
 let stok=Number(document.getElementById("stokBarang").value);

 if(!nama||harga<0||!Number.isInteger(stok)||stok<0||
 document.getElementById("hargaBarang").value===""||
 document.getElementById("stokBarang").value===""){
 alert("Lengkapi data barang dengan benar!");
 return;
 }

 produk.push({nama,harga,stok});
 simpan();
 tampilProduk();
 document.getElementById("namaBarang").value="";
 document.getElementById("hargaBarang").value="";
 document.getElementById("stokBarang").value="";
}

function hapusProduk(i){
 if(role!=="admin")return;
 if(confirm("Hapus barang ini?")){
 produk.splice(i,1);
 simpan();
 tampilProduk();
 }
}

function tampilProduk(){
 let tabel=document.getElementById("daftarBarang");
 let pilih=document.getElementById("pilihBarang");
 tabel.innerHTML="";
 pilih.innerHTML='<option value="">Pilih barang</option>';

 produk.forEach((p,i)=>{
 tabel.innerHTML+=`<tr>
 <td>${aman(p.nama)}</td><td>${rupiah(p.harga)}</td>
 <td>${p.stok}</td>
 <td>${role==="admin"?`<button class="danger" onclick="hapusProduk(${i})">Hapus</button>`:"-"}</td>
 </tr>`;
 pilih.innerHTML+=`<option value="${i}" ${p.stok<1?"disabled":""}>${aman(p.nama)} - ${rupiah(p.harga)}</option>`;
 });
}

function aman(s){
 return String(s).replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
}

function hitungTotal(){
 let i=document.getElementById("pilihBarang").value;
 let j=Number(document.getElementById("jumlah").value);
 let total=i===""?0:(produk[i].harga*j);
 document.getElementById("total").innerText=rupiah(total);
}

function prosesTransaksi(){
 let i=document.getElementById("pilihBarang").value;
 let j=Number(document.getElementById("jumlah").value);
 let bayar=Number(document.getElementById("bayar").value);
 let error=document.getElementById("error");
 error.innerText="";

 if(i===""||!Number.isInteger(j)||j<1){
 error.innerText="Pilih barang dan jumlah yang benar!";return;
 }

 let p=produk[i];
 let total=p.harga*j;

 if(j>p.stok){
 error.innerText="Stok barang tidak mencukupi!";return;
 }
 if(document.getElementById("bayar").value===""||bayar<total){
 error.innerText="Uang pembayaran kurang!";return;
 }

 let kembalian=bayar-total;
 p.stok-=j;

 let data={nama:p.nama,jumlah:j,harga:p.harga,total,bayar,kembalian,tanggal:new Date().toLocaleString("id-ID")};
 riwayat.unshift(data);
 simpan();
 tampilProduk();
 tampilRiwayat();

 document.getElementById("struk").innerHTML=`
 <div class="receipt">
 <h3 class="center">APLIKASI KASIR FARESI</h3>
 <p class="center">STRUK PEMBAYARAN</p>
 <hr>
 <p>Waktu: ${data.tanggal}</p>
 <p>Barang: ${aman(data.nama)}</p>
 <p>Harga: ${rupiah(data.harga)}</p>
 <p>Jumlah: ${data.jumlah}</p>
 <hr>
 <p>Total: <b>${rupiah(data.total)}</b></p>
 <p>Bayar: ${rupiah(data.bayar)}</p>
 <p>Kembalian: <b>${rupiah(data.kembalian)}</b></p>
 <hr><p class="center">Terima kasih telah berbelanja!</p>
 </div>`;

 document.getElementById("bayar").value="";
 document.getElementById("jumlah").value=1;
 document.getElementById("pilihBarang").value="";
 hitungTotal();
}

function tampilRiwayat(){
 document.getElementById("riwayat").innerHTML=riwayat.map(r=>`
 <tr><td>${aman(r.nama)}</td><td>${r.jumlah}</td>
 <td>${rupiah(r.total)}</td><td>${rupiah(r.kembalian)}</td></tr>
 `).join("");
}
</script>
</body>
</html>
<!doctype html><html lang="id" xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.org/extras/spring-security"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1"><title>Aplikasi Kasir Faresi</title><link rel="stylesheet" th:href="@{/css/style.css}"></head><body><header class="topbar"><div><b class="brand">Kasir Faresi</b><div class="muted">Pengelolaan penjualan dan stok barang</div></div><div class="user"><span>Login: <b sec:authentication="name">user</b></span><form th:action="@{/logout}" method="post"><button class="btn light">Keluar</button></form></div></header><main class="container"><p class="eyebrow">DASHBOARD</p><h1>Selamat datang di Aplikasi Kasir Faresi</h1><p class="muted">Kelola barang, catat transaksi, dan pantau penjualan.</p><div class="stats"><div class="stat">Total barang<strong th:text="${#lists.size(products)}">0</strong></div><div class="stat">Total transaksi<strong th:text="${#lists.size(sales)}">0</strong></div><div class="stat">Akumulasi penjualan<strong th:text="|Rp ${#numbers.formatDecimal(totalSales,0,'POINT',0,'COMMA')}|">Rp 0</strong></div></div><div th:if="${message}" class="success" th:text="${message}"></div><div th:if="${error}" class="alert" th:text="${error}"></div><div class="columns"><section class="panel"><h2>Tambah barang</h2><form th:action="@{/products}" method="post" class="stack"><label>Nama barang</label><input name="name" required placeholder="Contoh: Buku Tulis"><label>Harga (Rp)</label><input name="price" type="number" min="1" step="1" required placeholder="5000"><label>Stok</label><input name="stock" type="number" min="0" required placeholder="20"><button class="btn primary">Simpan barang</button></form></section><section class="panel"><h2>Transaksi penjualan</h2><form th:action="@{/sales}" method="post" class="stack"><label>Pilih barang</label><select name="productId" required><option value="">-- Pilih barang --</option><option th:each="p:${products}" th:value="${p.id}" th:disabled="${p.stock&lt;1}" th:text="|${p.name} - Rp ${#numbers.formatDecimal(p.price,0,'POINT',0,'COMMA')} (stok ${p.stock})|"></option></select><label>Jumlah beli</label><input name="quantity" type="number" min="1" value="1" required><label>Uang dibayar (Rp)</label><input name="paid" type="number" min="1" required placeholder="Uang dari pelanggan"><button class="btn primary">Simpan transaksi</button></form><p class="muted small">Total dan kembalian dihitung otomatis.</p></section></div><section class="panel"><h2>Daftar barang</h2><div class="table-wrap"><table><thead><tr><th>Nama</th><th>Harga</th><th>Stok</th><th>Aksi</th></tr></thead><tbody><tr th:each="p:${products}"><td th:text="${p.name}"></td><td th:text="|Rp ${#numbers.formatDecimal(p.price,0,'POINT',0,'COMMA')}|"></td><td th:text="${p.stock}"></td><td><form th:action="@{/products/{id}/delete(id=${p.id})}" method="post" onsubmit="return confirm('Hapus barang ini?')"><button class="btn danger">Hapus</button></form></td></tr><tr th:if="${#lists.isEmpty(products)}"><td colspan="4">Belum ada barang.</td></tr></tbody></table></div></section><section class="panel"><h2>Riwayat transaksi</h2><div class="table-wrap"><table><thead><tr><th>Waktu</th><th>Barang</th><th>Jumlah</th><th>Total</th><th>Bayar</th><th>Kembalian</th><th>Kasir</th></tr></thead><tbody><tr th:each="s:${sales}"><td th:text="${#temporals.format(s.createdAt,'dd-MM-yyyy HH:mm')}"></td><td th:text="${s.productName}"></td><td th:text="${s.quantity}"></td><td th:text="|Rp ${#numbers.formatDecimal(s.total,0,'POINT',0,'COMMA')}|"></td><td th:text="|Rp ${#numbers.formatDecimal(s.paid,0,'POINT',0,'COMMA')}|"></td><td th:text="|Rp ${#numbers.formatDecimal(s.changeAmount,0,'POINT',0,'COMMA')}|"></td><td th:text="${s.cashier}"></td></tr><tr th:if="${#lists.isEmpty(sales)}"><td colspan="7">Belum ada transaksi.</td></tr></tbody></table></div></section><footer>© Aplikasi Kasir Faresi</footer></main></body></html>
