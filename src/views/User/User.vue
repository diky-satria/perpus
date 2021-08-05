<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-8 user-peminjaman">
          <div class="back-user-peminjaman">
            <div class="front-user-peminjaman peminjaman-user">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Peminjaman Hari Ini</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3 style="font-weight:bold;">{{ pjmHariIni }}</h3>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-4 user-denda">
          <div class="back">
            <div class="front jurusan">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Total Transaksi ( Dipinjam )</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3 id="tot" style="font-weight:bold;">{{ totalTransPinjam }}</h3>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-4 user-laporan">
          <div class="back">
            <div class="front laporan-harian">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Total Buku ( Dipinjam )</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3 style="font-weight:bold;">{{ totalJumBuk }}</h3>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-8 user-card">
          <div class="card">
            <div class="card-header">
              Riwayat
            </div>
            <div class="card-body user-body-card overflow-auto">
              <li v-for="r in riwayat" :key="r.id" class="list-group-item">
                <router-link :to="{ name: 'detail.riwayat', params:{ kode:r.kode } }" class="d-flex justify-content-between" style="color:#707070">
                  <div class="col">
                    <div class="float-left">
                      <h6 style="font-size:16px;font-weight:bold;">{{ r.kode }}</h6>
                      <h6 style="font-size:12px;">{{ r.tgl_peminjaman }}</h6>
                      <h6 v-if="r.status === 1" class="badge badge-success mb-0" style="font-size:12px;">Dipinjam</h6>
                      <h6 v-else-if="r.status === 2" class="badge badge-dark mb-0" style="font-size:12px;">Dikembalikan</h6>
                    </div>
                  </div>
                  <div class="col">
                    <h6 class="float-right" style="font-size:16px;font-weight:bold;">{{ r.jumlah }}</h6>
                  </div>
                </router-link>
              </li>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { useLoading } from 'vue3-loading-overlay'
export default {
  setup(){
    const pjmHariIni = ref()
    const totalTransPinjam = ref()
    const totalJumBuk = ref()
    const riwayat = ref([])

    const getAPI = async () => {
      let loader = useLoading();
      loader.show({
        color: '#FFFFFF',
        isFullPage: 'true',
        backgroundColor: '#4B4B4B',
        loader: 'spinner',
        zIndex: 9999,
        width: 45,
        height: 45
      })
      let response = await axios.get('api/user')
      if(response.status === 200){
        pjmHariIni.value = response.data.pjmHariIni
        totalTransPinjam.value = response.data.totalTransPinjam
        totalJumBuk.value = response.data.totalJumBuk
        riwayat.value = response.data.riwayat
        loader.hide()
      }
    }

    onMounted(() => {
      getAPI()
    })

    return{
      pjmHariIni,
      getAPI,
      totalTransPinjam,
      totalJumBuk,
      riwayat
    }
  }
}
</script>