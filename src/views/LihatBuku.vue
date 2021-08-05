<template>
  <div>
    <div class="container">
      <div class="d-flex justify-content-between">
        <div>
          <div class="tombol-modify">List Buku</div>
        </div>
        <div class="float-right tom">
          <input type="text" class="form-control float-right" style="width:250px;border-radius:20px;" v-model="form.search" @keyup="getBuku()" placeholder="Cari judul...">
        </div>
      </div>
      <div class="row mt-4">
        <div class="col">
          <div class="container">
            <div class="row row-buku">
              <div v-for="b in buku" :key="b.id" class="card card-buku mb-3" style="width: 10rem;">
                <img :src="'http://localhost:8000/storage/buku/' + b.gambar" class="card-img-top card-gambar" style="height:150px;">
                <div class="card-body text-center py-1 px-2">
                  <p style="font-size:13px;" class="card-text my-1">{{ b.kode }}</p>
                  <h5 style="font-size:15px;" class="card-title mb-1">{{ b.judul }}</h5>
                  <p v-if="b.jumlah <= 0" style="font-size:13px;text-decoration:line-through;color:red" class="card-text my-0">Stok : {{ b.jumlah }}</p>
                  <p v-else: style="font-size:13px;" class="card-text my-0">Stok : {{ b.jumlah }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row mt-3">
        <div class="col-md">
          <nav class="d-flex justify-content-center">
            <ul class="pagination">
              <li v-bind:class="{disabled:!pagination.first_link}" class="page-item"><a @click="getBuku(pagination.first_link)" href="#" class="page-link">&laquo;</a></li>
              <li v-bind:class="{disabled:!pagination.prev_link}" class="page-item"><a @click="getBuku(pagination.prev_link)" href="#" class="page-link">&lt;</a></li>
              <li v-for="n in pagination.last_page" :key="n" v-bind:class="{active: pagination.current_page == n}" class="page-item"><a @click="getBuku(pagination.path_page + n)" href="#" class="page-link">{{ n }}</a></li>
              <li v-bind:class="{disabled:!pagination.next_link}" class="page-item"><a @click="getBuku(pagination.next_link)" href="#" class="page-link">&gt;</a></li>
              <li v-bind:class="{disabled:!pagination.last_link}" class="page-item"><a @click="getBuku(pagination.last_link)" href="#" class="page-link">&raquo;</a></li>
            </ul>
          </nav>
        </div>
        <!-- <div class="col-md-4">
          Page: {{ pagination.current_page }} - {{ pagination.to_page }}
          Total: {{ pagination.total_page }}
        </div> -->
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { ref, onMounted, reactive } from 'vue'
export default {
  setup(){
    const buku = ref([])
    const pagination = ref({})
    const form = reactive({
      search : ''
    })

    const getBuku = async (pagi) => {
      pagi = pagi || 'api/bukuHome?search=' + form.search
      let response = await axios.get(pagi)
      if(response.status === 200){
        buku.value = response.data.data
        pagination.value = {
          current_page: response.data.meta.current_page,
          last_page: response.data.meta.last_page,
          from_page: response.data.meta.from,
          to_page: response.data.meta.to,
          total_page: response.data.meta.total,
          path_page: response.data.meta.path+"?page=",
          first_link: response.data.links.first,
          last_link: response.data.links.last,
          prev_link: response.data.links.prev,
          next_link: response.data.links.next,
        }
      }
    }

    onMounted(() => {
      getBuku()
    })

    return {
      buku,
      getBuku,
      pagination,
      form
    }
  }
}
</script>