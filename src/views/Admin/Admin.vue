<template>
  <div class="admin">
    <div class="container mt-4">
      <div class="row row-admin">
        <div class="col-md">
          <div class="back">
            <div class="front mahasiswa">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Data Mahasiswa</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3 style="font-weight:bold;">{{ mahasiswas }}</h3>
                </div>
                <div class="col">
                  <router-link to="/mahasiswa" title="Lihat" class="float-right text-white text-decoration-none"><i class="fas fa-arrow-right"></i></router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md">
          <div class="back">
            <div class="front buku">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Data Buku</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3 style="font-weight:bold;">{{ bukus }}</h3>
                </div>
                <div class="col">
                  <router-link to="/buku" title="Lihat" class="float-right text-white text-decoration-none"><i class="fas fa-arrow-right"></i></router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md">
          <div class="back">
            <div class="front jurusan">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Data Jurusan</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3 style="font-weight:bold;">{{ jurusans }}</h3>
                </div>
                <div class="col">
                  <router-link to="/jurusan" title="Lihat" class="float-right text-white text-decoration-none"><i class="fas fa-arrow-right"></i></router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-4">
          <div class="back">
            <div class="front collapse-transaksi">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Transaksi Collapse</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3 style="font-weight:bold;">{{ transaksi }}</h3>
                </div>
                <div class="col">
                  <router-link to="/transaksi.collapse" title="Lihat" class="float-right text-white text-decoration-none"><i class="fas fa-arrow-right"></i></router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="back">
            <div class="front laporan-harian">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Laporan Harian</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3>
                    <i class="fas fa-calendar-day"></i>
                  </h3>
                </div>
                <div class="col">
                  <router-link to="/laporan.harian" title="Lihat" class="float-right text-white text-decoration-none"><i class="fas fa-arrow-right"></i></router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="back">
            <div class="front laporan-bulanan">
              <div class="row">
                <div class="col">
                  <h6 class="text-uppercase">Laporan Bulanan</h6>
                </div>
              </div>
              <div class="row">
                <div class="col">
                  <h3>
                    <i class="fas fa-calendar-alt"></i>
                  </h3>
                </div>
                <div class="col">
                  <router-link to="/laporan.bulanan" title="Lihat" class="float-right text-white text-decoration-none"><i class="fas fa-arrow-right"></i></router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md mt-4 row justify-content-center">
          <ChartLine v-if="loaded" v-bind:chartDataLine="chartdataline" v-bind:chartOptionsLine="chartoptionsline" />
        </div>
        <div class="col-md mt-4 row justify-content-center">
          <ChartPie v-if="loaded" v-bind:chartDataPie="chartdatapie" v-bind:chartOptionsPie="chartoptionspie" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { useLoading } from 'vue3-loading-overlay'
import ChartLine from '@/components/ChartLine.vue'
import ChartPie from '@/components/ChartPie.vue'
export default {
  components:{
    ChartLine,
    ChartPie
  },
  data(){
    return{
      mahasiswas: '',
      jurusans: '',
      bukus: '',
      transaksi: '',

      //chartline data
      chartdataline: null,
      chartoptionsline: null,
      datalinetgl: [],
      datalinejumlah: [],
      loaded: false,

      //chartpie
      chartdatapie: null,
      chartoptionspie: null,
      datapiejudul: [],
      datapiejumlah: [],
    }
  },
  mounted(){
    this.getCount()
  },
  methods:{
    async getCount(){
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
      let response = await axios.get('api/count')
      if(response.status === 200){
        this.mahasiswas = response.data.mahasiswa
        this.jurusans = response.data.jurusan
        this.bukus = response.data.buku
        this.transaksi = response.data.transaksi
        loader.hide()

        this.loaded = true

        //chartdataline
        this.datalinetgl = response.data.chartLine.map((x) => x.tgl_peminjaman)
        this.datalinejumlah = response.data.chartLine.map((x) => x.jumlah)
        this.chartdataline = {
          labels: this.datalinetgl.reverse(),
          datasets: [
            {
              label: 'Jumlah Buku Dipinjam',
              data: this.datalinejumlah.reverse(),
              borderColor: 'red',
              borderWidth: 1,
              pointBackgroundColor: 'red'
            }
          ]
        },
        this.chartoptionsline = {
          scales: {
            yAxes: [
              {
                ticks: {
                  beginAtZero: true
                }
              }
            ]
          },
          maintainAspecRation: false,
          title: {
            display: true,
            text: 'Peminjaman 15 Hari Terakhir'
          },
          responsive: false
        }

        //chartdatapie
        this.datapiejudul = response.data.chartPie.map((x) => x.judul)
        this.datapiejumlah = response.data.chartPie.map((x) => x.jumlahPinjam)
        this.chartdatapie = {
          labels: this.datapiejudul,
          datasets: [
            {
              label: 'Jumlah Buku Dipinjam',
              data: this.datapiejumlah,
              borderColor: 'white',
              backgroundColor: ['red', 'green', 'blue', 'yellow', 'black']
            }
          ]
        },
        this.chartoptionspie = {
          title: {
            display: true,
            text: 'Buku Paling Sering Dipinjam'
          },
          responsive: false
        }
      }
    }
  }
}
</script>