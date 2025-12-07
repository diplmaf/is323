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
        <select id="maskSelect" v-model="mask">
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
        <span class="result-value">{{ mask }}</span>
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
const mask = ref(options[0]);
const showResult = ref(false);

const isIpValidComputed = computed(() => isIpValid(ip.value));

const networkAddress = computed(() => {
  if (!isIpValidComputed.value) return "";
  return getNetworkAddress(ip.value, mask.value);
});

const addressesCount = computed(() => {
  if (!isIpValidComputed.value) return "";
  return getAddressesCount(mask.value);
});

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
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
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
  color: #333;
}

.input-group input,
.input-group select {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
}

.input-group input.input-invalid {
  border-color: #e74c3c;
  background-color: #fadbd8;
}

.btn-disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

button {
  padding: 10px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover:not(.btn-disabled) {
  background-color: #2980b9;
}

.result-container {
  margin-top: 20px;
  padding: 15px;
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.result-item {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
  border-bottom: 1px solid #eee;
}

.result-item:last-child {
  border-bottom: none;
}

.result-label {
  font-weight: bold;
  color: #555;
}

.result-value {
  color: #333;
}
</style>
