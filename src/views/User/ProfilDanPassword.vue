<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-8 mb-4">
          <div class="d-flex justify-content-between">
            <div>
              <div class="tombol-modify">Profil</div>
            </div>
          </div>
          <div class="card" v-if="authenticated">
            <div class="card-body">
              <div class="row">
                <div class="col-md">
                  <table class="table-sm">
                      <tr>
                        <td>Nama</td>
                        <td>:</td>
                        <td>{{ user.nama }}</td>
                      </tr>
                      <tr>
                        <td>Email</td>
                        <td>:</td>
                        <td>{{ user.email }}</td>
                      </tr>
                      <template v-if="authenticated && user.role === 'user'">
                        <tr>
                          <td>Jurusan</td>
                          <td>:</td>
                          <td>{{ mhs.jurusan }}</td>
                        </tr>
                        <tr>
                          <td>Semester</td>
                          <td>:</td>
                          <td>{{ mhs.semester }}</td>
                        </tr>
                        <tr>
                          <td>Kelas</td>
                          <td>:</td>
                          <td>{{ mhs.kelas }}</td>
                        </tr>
                      </template>
                  </table>
                </div>
                <div class="col-md" v-if="authenticated && user.role === 'user'">
                  <img :src="'http://localhost:8000/storage/mahasiswa/' + mhs.gambar" style="width:100%;height:242px;">
                  <form @submit.prevent="ubahProfil">
                    <div class="form-group">
                      <div class="d-flex flex-column">
                        <img :src="fileURL" v-show="fileURL" style="width:100%;height:242px;" class="mt-3">
                        <div class="custom-file my-3">
                          <input type="file" accept="image/*" @change="ubahGambar" class="custom-file-input" id="customFile">
                          <label class="custom-file-label" for="customFile">Ubah Profil</label>
                        </div>
                      </div>
                      <button type="submit" class="btn btn-sm btn-primary btn-block d-flex justify-content-center" id="ubah-gambar" disabled>
                        <div>
                          Ubah Profil
                        </div>
                        <div>
                          <svg v-if="load" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="margin: auto; background: rgba(255, 255, 255, 0); display: block; shape-rendering: auto;" width="24px" height="22px" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid">
                            <g>
                              <path d="M50 15A35 35 0 1 0 74.74873734152916 25.251262658470843" fill="none" stroke="#ffffff" stroke-width="12"></path>
                              <path d="M49 3L49 27L61 15L49 3" fill="#ffffff"></path>
                              <animateTransform attributeName="transform" type="rotate" repeatCount="indefinite" dur="1s" values="0 50 50;360 50 50" keyTimes="0;1"></animateTransform>
                            </g>
                          </svg>
                        </div>
                      </button>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-4">
          <div class="d-flex justify-content-between">
            <div>
              <div class="tombol-modify">Ubah Password</div>
            </div>
          </div>
          <div class="card">
            <div class="card-body">
              <form @submit.prevent="ubahPassword">
                <div class="form-group">
                  <label>Password</label>
                  <input type="password" v-model="ubah.password" class="form-control">
                  <span class="text-danger" v-if="errors['password']">{{ errors['password'][0] }}</span>
                </div>
                <div class="form-group">
                  <label>Konfirmasi Password</label>
                  <input type="password" v-model="ubah.konfirmasi_password" class="form-control">
                  <span class="text-danger" v-if="errors['konfirmasi_password']">{{ errors['konfirmasi_password'][0] }}</span>
                </div>
                <button type="submit" class="btn btn-sm btn-primary float-right d-flex justify-content-center" id="btn-profil">
                  <div>
                    Ubah
                  </div>
                  <div>
                    <svg v-if="loadPassword" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="margin: auto; background: rgba(255, 255, 255, 0); display: block; shape-rendering: auto;" width="24px" height="22px" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid">
                      <g>
                        <path d="M50 15A35 35 0 1 0 74.74873734152916 25.251262658470843" fill="none" stroke="#ffffff" stroke-width="12"></path>
                        <path d="M49 3L49 27L61 15L49 3" fill="#ffffff"></path>
                        <animateTransform attributeName="transform" type="rotate" repeatCount="indefinite" dur="1s" values="0 50 50;360 50 50" keyTimes="0;1"></animateTransform>
                      </g>
                    </svg>
                  </div>
                </button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import store from '@/store'
import { computed, ref, onMounted, watch, reactive } from 'vue'
import axios from 'axios'
import Swal from 'sweetalert2'
import { useLoading } from 'vue3-loading-overlay'
export default {
  setup(){
    const authenticated = computed(() => store.getters["auth/authenticated"])
    const user = computed(() => store.getters["auth/user"])
    const mhs = ref([])
    const fileGambar = ref('')
    const fileURL = ref('')
    const form = reactive({
      gambar: ''
    })
    const load = ref(false)
    const ubah = reactive({
      password: '',
      konfirmasi_password:''
    })
    const loadPassword = ref(false)
    const errors = ref([])

    //sweetalert
    const Toast = Swal.mixin({
      toast: true,
      position: 'top-end',
      showConfirmButton: false,
      timer: 5000,
      timerProgressBar: true,
      didOpen: (toast) => {
        toast.addEventListener('mouseenter', Swal.stopTimer)
        toast.addEventListener('mouseleave', Swal.resumeTimer)
      },
      iconColor: 'green',
      background: 'rgb(91, 255, 96)'
    })

    const getMahasiswa = async () => {
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
      let response = await axios.get('api/meUser')
      if(response.status === 200){
        mhs.value = response.data.mahasiswa
        loader.hide()
      }
    }

    //ubah gambar
    const ubahGambar = (e) => {
      let dis = document.getElementById('ubah-gambar')
      if(e.target.files.length === 0){
        fileGambar.value = ''
        fileURL.value = ''
        dis.setAttribute('disabled', true)
        return
      }
      fileGambar.value = e.target.files[0]
      dis.removeAttribute('disabled', false)
    }
    
    watch(fileGambar, (fileGambar) => {
      if(!(fileGambar instanceof File)){
        return
      }
      const fileReader = new FileReader()

      fileReader.readAsDataURL(fileGambar)

      fileReader.addEventListener("load", () => {
        fileURL.value = fileReader.result
      })
    })

    const ubahProfil = async () => {
      load.value = true
      let data = new FormData()
      data.append('gambar', fileGambar.value)
      let response = await axios.post('api/mahasiswa/ubahProfil/', data)
      if(response.status === 200){
        fileGambar.value = ''
        fileURL.value = ''
        let dis = document.getElementById('ubah-gambar')
        dis.setAttribute('disabled', true)
        getMahasiswa()
        load.value = false
        setTimeout(() => {
          Toast.fire({
            icon: 'success',
            title: 'Profil berhasil diubah'
          })
        }, 2000)
      }
    }

    const ubahPassword = async () => {
      let btn = document.getElementById('btn-profil')
      try{
        btn.setAttribute('disabled', true)
        loadPassword.value = true
        let response = await axios.patch('api/mahasiswa/ubahPassword/' + store.getters["auth/user"].id, ubah)
        if(response.status === 200){
          ubah.password = ''
          ubah.konfirmasi_password = ''
          errors.value.password = ''
          errors.value.konfirmasi_password = ''
          loadPassword.value = false
          btn.removeAttribute('disabled', false)
           Toast.fire({
            icon: 'success',
            title: 'Password berhasil diubah'
          })
        }
      }catch(e){
        loadPassword.value = true
        errors.value = e.response.data.errors
        loadPassword.value = false
        btn.removeAttribute('disabled', false)
      }
    }

    onMounted(() => {
      getMahasiswa()
    })

    return {
      authenticated,
      user,
      mhs,
      getMahasiswa,
      ubahGambar,
      fileGambar,
      fileURL,
      form,
      ubahProfil,
      Toast,
      load,
      ubah,
      ubahPassword,
      loadPassword,
      errors
    }
  }
}
</script>