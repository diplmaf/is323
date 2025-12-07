<template>
  <div class="subnet-calculator">
    <form @submit.prevent="calculate" class="calculator-form">
      <div class="input-group">
        <label for="ipInput">IP Адрес:</label>
        <input
          id="ipInput"
          v-model="ip"
          type="text"
          placeholder="Например: 192.168.1.150"
          :class="{ 'input-invalid': !isIpValid && ip !== '' }"
        />
      </div>

      <div class="input-group">
        <label for="maskSelect">Маска подсети:</label>
        <select id="maskSelect" v-model="selectedMask" @change="onMaskChange">
          <option v-for="option in options" :key="option" :value="option">
            {{ option }}
          </option>
        </select>
      </div>

      <button
        type="submit"
        :disabled="!isIpValid || !ip"
        :class="{ 'btn-disabled': !isIpValid || !ip }"
      >
        Рассчитать
      </button>
    </form>

    <div v-if="showResult" class="result-container">
      <div class="result-item">
        <span class="result-label">Введенный IP адрес:</span>
        <span class="result-value">{{ ip }}</span>
      </div>
      <div class="result-item">
        <span class="result-label">Выбранная маска:</span>
        <span class="result-value">{{ selectedMask }}</span>
      </div>
      <div class="result-item">
        <span class="result-label">Адрес сети:</span>
        <span class="result-value">{{ networkAddress }}</span>
      </div>
      <div class="result-item">
        <span class="result-label">Количество возможных адресов:</span>
        <span class="result-value">{{ addressesCount }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import { options } from "@/options";
import {
  isIpValid,
  getNetworkAddress,
  getAddressesCount,
} from "@/functions";

const ip = ref("");
const selectedMask = ref(options[0]);
const showResult = ref(false);

const isIpValidComputed = computed(() => isIpValid(ip.value));

const networkAddress = computed(() => {
  if (!isIpValidComputed.value || !showResult.value) return "";
  return getNetworkAddress(ip.value, selectedMask.value);
});

const addressesCount = computed(() => {
  if (!isIpValidComputed.value || !showResult.value) return "";
  return getAddressesCount(selectedMask.value);
});

function onMaskChange() {
  showResult.value = false;
}

function calculate() {
  if (isIpValidComputed.value && ip.value) {
    showResult.value = true;
  }
}
</script>

<style scoped>
.subnet-calculator {
  max-width: 500px;
  margin: 20px auto;
  padding: 20px;
  background: linear-gradient(135deg, #e0eafc, #cfdef3);
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

.calculator-form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.input-group {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.input-group label {
  font-weight: bold;
  color: #2c3e50;
}

.input-group input,
.input-group select {
  padding: 10px;
  border: 2px solid #a0b3c6;
  border-radius: 6px;
  font-size: 16px;
  background-color: #ffffff;
  transition: border-color 0.3s ease;
}

.input-group input:focus,
.input-group select:focus {
  outline: none;
  border-color: #3498db;
}

.input-group input.input-invalid {
  border-color: #e74c3c;
  background-color: #fadbd8;
}

.btn-disabled {
  background-color: #bdc3c7;
  cursor: not-allowed;
}

button {
  padding: 12px;
  background: linear-gradient(to right, #3498db, #2980b9);
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  cursor: pointer;
  transition: background 0.3s ease;
}

button:hover:not(.btn-disabled) {
  background: linear-gradient(to right, #2980b9, #1c5a85);
}

.result-container {
  margin-top: 20px;
  padding: 15px;
  background-color: rgba(255, 255, 255, 0.8);
  border: 1px solid #b3d9ff;
  border-radius: 8px;
  box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
}

.result-item {
  display: flex;
  justify-content: space-between;
  padding: 10px 0;
  border-bottom: 1px dashed #d6eaf8;
}

.result-item:last-child {
  border-bottom: none;
}
  .result-label {
  font-weight: bold;
  color: #2c3e50;
}

.result-value {
  color: #27ae60;
  font-weight: 500;
}
</style>
