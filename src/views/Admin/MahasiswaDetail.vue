<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-6">
          <div class="d-flex justify-content-between">
            <div>
              <div class="tombol-modify">Detail Mahasiswa</div>
            </div>
            <div class="float-right tom">
              <router-link to="/mahasiswa" title="Kembali" class="btn btn-sm btn-dark"><i class="fas fa-arrow-left"></i></router-link>
            </div>
          </div>
          <div class="card">
            <div class="card-body">
              <form>
                <div class="form-group row">
                  <label for="staticEmail" class="col-3 col-form-label">NIM</label>
                  <div class="col-9">
                    <div class="form-control-plaintext">{{ state.nim }}</div>
                  </div>
                </div>
                <div class="form-group row">
                  <label for="staticEmail" class="col-3 col-form-label">Nama</label>
                  <div class="col-9">
                    <div class="form-control-plaintext">{{ state.nama }}</div>
                  </div>
                </div>
                <div class="form-group row">
                  <label for="staticEmail" class="col-3 col-form-label">Email</label>
                  <div class="col-9">
                    <div class="form-control-plaintext">{{ state.email }}</div>
                  </div>
                </div>
                <div class="form-group row">
                  <label for="staticEmail" class="col-3 col-form-label">Jurusan</label>
                  <div class="col-9">
                    <div class="form-control-plaintext">{{ state.jurusan }}</div>
                  </div>
                </div>
                <div class="form-group row">
                  <label for="staticEmail" class="col-3 col-form-label">Semester</label>
                  <div class="col-9">
                    <div class="form-control-plaintext">{{ state.semester }}</div>
                  </div>
                </div>
                <div class="form-group row">
                  <label for="staticEmail" class="col-3 col-form-label">Kelas</label>
                  <div class="col-9">
                    <div class="form-control-plaintext">{{ state.kelas }}</div>
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { useRoute } from 'vue-router'
import { useLoading } from 'vue3-loading-overlay'
export default {
  setup(){
    const state = ref([])
    const par = useRoute()

    const getDetail = async () => {
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
      let response = await axios.get('api/mahasiswa/' + par.params.id)
      state.value = response.data.data
      loader.hide()
    }

    onMounted(() => {
      getDetail()
    })

    return {
      state,
      getDetail,
      par
    }
  }
}
</script>