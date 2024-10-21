<template>
  <nav>
    <router-link to="/">Penelian</router-link> |
    <router-link to="/about">Absensi</router-link>
  </nav>
  <router-view/>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
nav {
  padding: 15px 30px; /* Padding atas dan bawah, serta kiri dan kanan */
  display: flex; /* Menggunakan flexbox untuk tata letak yang lebih baik */
  justify-content: center; /* Menempatkan item di tengah */
}

nav a {
  text-decoration: none;
  font-weight: bold;
  color: #2c3e50; /* Warna teks utama */
  margin: 0 15px; /* Jarak antar link */
  text-decoration: none; /* Menghilangkan garis bawah pada link */
  transition: color 0.3s; /* Efek transisi untuk hover */
}

nav a:hover {
  color: #0c95a7; /* Warna saat hover */
  text-decoration: none; /* Garis bawah saat hover */
}

nav a.router-link-exact-active {
  color: #2812f3; /* Warna untuk link aktif */
  text-decoration: none; /* Garis bawah untuk link aktif */
}
</style>
