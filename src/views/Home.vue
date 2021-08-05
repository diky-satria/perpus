<template>
  <div class="home">
    <div class="container">
      <div class="row">
        <div class="col-lg-6 kiri">
          <h2 class="selamat">Selamat datang di web<br> perpustakaan <b>UNUSIA</b></h2>
          <p>Membantu anda melakukan pencarian, peminjaman,<br> 
          dan pengembalian buku dengan mudah dan aman</p>
          <div class="d-flex tom">
            <router-link to="/lihat-buku" class="nav-link lihat-buku text-white">Lihat buku</router-link>
            <router-link v-if="!authenticated" to="/login" class="nav-link ml-3 tombol-login">Login</router-link>
          </div> 
        </div>
        <div class="col-lg-6 home-background">
          <img src="../assets/home.png"> 
        </div> 
      </div>
    </div>
  </div> 
</template>

<script>
import store from '@/store'
import { computed } from 'vue'
export default {
  setup(){
     const authenticated = computed(() => store.getters["auth/authenticated"])
     
     return {
       authenticated
     }
  }
}
</script>