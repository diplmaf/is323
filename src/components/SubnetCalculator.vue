<template>
  <div class="calculator">
    <h2>Калькулятор подсетей</h2>
    
    <div class="form-group">
      <label>IP адрес:</label>
      <input 
        v-model="ip" 
        type="text" 
        placeholder="192.168.1.150"
        :class="{ 'error': ip && !isValid }"
        @keyup.enter="calculate"
      >
      <div v-if="ip && !isValid" class="error-message">
        Неверный формат IP адреса
      </div>
    </div>
    
    <div class="form-group">
      <label>Маска подсети:</label>
      <select v-model="selectedMask">
        <option 
          v-for="mask in NETWORK_MASKS" 
          :key="mask" 
          :value="mask"
        >
          {{ mask }}
        </option>
      </select>
    </div>
    
    <button 
      @click="calculate" 
      :disabled="!isValid || !ip"
      class="calculate-btn"
    >
      Рассчитать
    </button>
    
    <div v-if="showResults" class="results">
      <h3>Результаты расчета:</h3>
      
      <div class="result-row">
        <span class="label">IP адрес:</span>
        <span class="value">{{ calculatedIp }}</span>
      </div>
      
      <div class="result-row">
        <span class="label">Маска подсети:</span>
        <span class="value">{{ calculatedMask }}</span>
      </div>
      
      <div class="result-row">
        <span class="label">Адрес сети:</span>
        <span class="value">{{ networkAddress }}</span>
      </div>
      
      <div class="result-row">
        <span class="label">Количество адресов:</span>
        <span class="value">{{ addressesCount }}</span>
      </div>
      
      <div class="result-row">
        <span class="label">Широковещательный адрес:</span>
        <span class="value">{{ broadcastAddress }}</span>
      </div>
      
      <div class="result-row">
        <span class="label">Первый доступный адрес:</span>
        <span class="value">{{ firstAddress }}</span>
      </div>
      
      <div class="result-row">
        <span class="label">Последний доступный адрес:</span>
        <span class="value">{{ lastAddress }}</span>
      </div>
      
      <div class="result-row">
        <span class="label">Диапазон адресов:</span>
        <span class="value">{{ firstAddress }} - {{ lastAddress }}</span>
      </div>
      
      <button @click="clearResults" class="clear-btn">
        Очистить результаты
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { NETWORK_MASKS } from '../constants/options'
import { 
  isIpValid, 
  getNetworkAddress, 
  getAddressesCount,
  getBroadcastAddress,
  getFirstAddress,
  getLastAddress 
} from '../utils/functions'

const ip = ref('192.168.1.150')
const selectedMask = ref(NETWORK_MASKS[24])

const showResults = ref(false)

const calculatedIp = ref('')
const calculatedMask = ref('')

const isValid = computed(() => isIpValid(ip.value))

const networkAddress = computed(() => 
  calculatedIp.value && calculatedMask.value 
    ? getNetworkAddress(calculatedIp.value, calculatedMask.value)
    : ''
)

const addressesCount = computed(() => 
  calculatedMask.value ? getAddressesCount(calculatedMask.value) : 0
)

const broadcastAddress = computed(() => 
  calculatedIp.value && calculatedMask.value 
    ? getBroadcastAddress(calculatedIp.value, calculatedMask.value)
    : ''
)

const firstAddress = computed(() => 
  calculatedIp.value && calculatedMask.value 
    ? getFirstAddress(calculatedIp.value, calculatedMask.value)
    : ''
)

const lastAddress = computed(() => 
  calculatedIp.value && calculatedMask.value 
    ? getLastAddress(calculatedIp.value, calculatedMask.value)
    : ''
)
function calculate() {
  if (isValid.value && ip.value) {
 
    calculatedIp.value = ip.value
    calculatedMask.value = selectedMask.value

    showResults.value = true
  }
}

function clearResults() {
  showResults.value = false
  calculatedIp.value = ''
  calculatedMask.value = ''
}
</script>

<style scoped>

.clear-btn {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #ff4444;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.clear-btn:hover {
  background-color: #cc0000;
}
</style>
