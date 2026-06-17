<template>
  <div class="p-6 max-w-7xl mx-auto">
    <div class="flex items-center justify-between mb-8">
      <div>
        <h1 class="text-2xl font-bold text-gray-800">Katalog Materi Pembelajaran</h1>
        <p class="text-gray-500 mt-1">Pilih dan adopsi materi dari pusat untuk kelas Anda</p>
      </div>
    </div>

    <!-- Simulasi Pilih Kelas -->
    <el-card class="mb-6 border-0 shadow-sm">
      <div class="flex items-center gap-4">
        <span class="font-medium">Pilih Kelas:</span>
        <el-select v-model="selectedKelas" placeholder="Pilih Kelas" style="width: 240px">
          <el-option label="X IPA 1" :value="1" />
          <el-option label="X IPA 2" :value="2" />
        </el-select>
      </div>
    </el-card>

    <div v-loading="loading">
      <el-collapse v-model="activeNames">
        <el-collapse-item 
          v-for="pelajaran in katalogData" 
          :key="pelajaran.id" 
          :title="`${pelajaran.nama} - ${pelajaran.tingkat}`" 
          :name="pelajaran.id"
        >
          <div class="p-4 bg-gray-50 rounded-lg">
            
            <div v-for="bab in pelajaran.babs" :key="bab.id" class="mb-6 bg-white p-4 rounded-lg shadow-sm border">
              <h3 class="text-lg font-bold text-indigo-700 border-b pb-2 mb-4">Bab: {{ bab.judul }}</h3>
              
              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <div v-for="subBab in bab.sub_babs" :key="subBab.id" class="border rounded p-4 hover:shadow-md transition">
                  <div class="flex justify-between items-start mb-2">
                    <h4 class="font-semibold text-gray-800">{{ subBab.judul }}</h4>
                    <el-button type="success" size="small" plain @click="adopsiMateri(subBab.id)" :disabled="!selectedKelas">
                      Adopsi (Gunakan)
                    </el-button>
                  </div>
                  
                  <p class="text-sm text-gray-500 mb-3">Terdapat {{ subBab.kontens?.length || 0 }} Konten Belajar</p>
                  
                  <ul class="text-xs space-y-1">
                    <li v-for="konten in subBab.kontens" :key="konten.id" class="flex items-center gap-2">
                      <el-tag size="small" :type="konten.tipe === 'video' ? 'primary' : 'warning'">{{ konten.tipe }}</el-tag>
                      <span class="truncate">{{ konten.judul }}</span>
                    </li>
                  </ul>
                </div>
              </div>

            </div>

          </div>
        </el-collapse-item>
      </el-collapse>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { ElMessage } from 'element-plus'
import axiosInstance from '@/api/axios'

const katalogData = ref([])
const loading = ref(true)
const activeNames = ref([])
const selectedKelas = ref(null)

const fetchKatalog = async () => {
  loading.value = true
  try {
    const response = await axiosInstance.get('/guru/katalog')
    katalogData.value = response.data.data
    // Auto expand the first item
    if (katalogData.value.length > 0) {
      activeNames.value = [katalogData.value[0].id]
    }
  } catch (error) {
    ElMessage.error('Gagal mengambil data katalog')
  } finally {
    loading.value = false
  }
}

const adopsiMateri = async (subBabId) => {
  if (!selectedKelas.value) {
    ElMessage.warning('Silakan pilih kelas terlebih dahulu')
    return
  }

  try {
    await axiosInstance.post('/guru/katalog/adopsi', {
      kelas_id: selectedKelas.value,
      sub_bab_id: subBabId
    })
    ElMessage.success('Berhasil mengadopsi materi untuk kelas ini!')
  } catch (error) {
    ElMessage.error('Gagal mengadopsi materi')
  }
}

onMounted(() => {
  fetchKatalog()
})
</script>
