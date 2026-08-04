<script setup>
import { ref } from 'vue'

const billAmount = ref('')
const lastTipAmount = ref('')
const totalTips = ref('')
const accumulatedTip = ref(0)
const activeTipId = ref('')

const tipOptions = [
  { id: '24month', label: '5%', value: 0.05 },
  { id: '18month', label: '15%', value: 0.15 },
  { id: '12month', label: '20%', value: 0.2 },
  { id: 'quarterly', label: '25%', value: 0.25 },
  { id: 'monthly', label: '50%', value: 0.5 },
]

function parseAmount(value) {
  const normalized = String(value).replace(/[^\d,.-]/g, '').replace(',', '.')
  const parsed = Number.parseFloat(normalized)
  return Number.isFinite(parsed) ? parsed : 0
}

function formatCurrency(value) {
  return `$${value.toFixed(2)}`
}

function calculateTip(percent, tipId) {
  const amount = parseAmount(billAmount.value)

  if (amount <= 0) {
    accumulatedTip.value = 0
    lastTipAmount.value = ''
    totalTips.value = ''
    activeTipId.value = ''
    return
  }

  const tip = amount * percent
  accumulatedTip.value += tip
  lastTipAmount.value = formatCurrency(tip)
  totalTips.value = formatCurrency(accumulatedTip.value)
  billAmount.value = ''
  activeTipId.value = tipId
}

function resetForm() {
  billAmount.value = ''
  lastTipAmount.value = ''
  totalTips.value = ''
  accumulatedTip.value = 0
  activeTipId.value = ''
}

const buttonClass = (tipId) => [
  'btn',
  'btn-primary',
  'btn-lg',
  'btn-block',
  { active: activeTipId.value === tipId },
]
</script>

<template>
  <div class="container">
    <div class="price-box">
      <div class="row">
        <div class="col-sm-6">
          <form class="form-horizontal form-pricing" role="form" @submit.prevent>
            <div class="price-slider">
              <h4 class="great">Conta(R$)</h4>
              <input
                id="comandaTotal"
                v-model="billAmount"
                class="form-control"
                placeholder="Valor"
                type="text"
              >
            </div>

            <div class="price-slider">
              <h4 class="great">Gorjeta %</h4>

              <div class="btn-group btn-group-justified">
                <div v-for="option in tipOptions.slice(0, 3)" :key="option.id" class="btn-group btn-group-lg">
                  <button
                    :id="option.id"
                    :class="buttonClass(option.id)"
                    :value="option.value"
                    type="button"
                    @click="calculateTip(option.value, option.id)"
                  >
                    {{ option.label }}
                  </button>
                </div>
              </div>
            </div>

            <div class="price-slider">
              <div class="btn-group btn-group-justified">
                <div v-for="option in tipOptions.slice(3)" :key="option.id" class="btn-group btn-group-lg">
                  <button
                    :id="option.id"
                    :class="buttonClass(option.id)"
                    :value="option.value"
                    type="button"
                    @click="calculateTip(option.value, option.id)"
                  >
                    {{ option.label }}
                  </button>
                </div>
              </div>
            </div>
          </form>
        </div>

        <div class="col-sm-6">
          <div class="price-form">
            <div class="form-group">
              <div class="row">
                <div class="col-sm-6">
                  <label for="tipAmount" class="control-label">Gorjeta($): </label>
                  <span class="help-text">Ultima gorjeta recebida</span>
                </div>
                <div class="col-sm-6">
                  <input id="tipAmount" v-model="lastTipAmount" class="form-control" type="text" readonly>
                </div>
              </div>
            </div>

            <div class="form-group">
              <div class="row">
                <div class="col-sm-6">
                  <label for="total" class="control-label">Gorjeta total($): </label>
                  <span class="help-text">Total de gorjetas</span>
                </div>
                <div class="col-sm-6">
                  <input id="total" v-model="totalTips" class="form-control" type="text" readonly>
                </div>
              </div>
            </div>

            <div style="margin-top:30px"></div>
            <hr class="style">

            <div class="form-group">
              <div class="col-sm-12">
                <button id="reset" class="btn btn-primary btn-lg btn-block" type="button" @click="resetForm">
                  Reset
                </button>
              </div>
            </div>

            <div class="form-group">
              <div class="col-sm-12">
                <img
                  alt="Payment"
                  class="img-responsive payment"
                  src="https://github.com/AmirolAhmad/Bootstrap-Calculator/blob/master/images/payment.png?raw=true"
                >
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.price-box {
  margin: 0 auto;
  background: #e9e9e9;
  border-radius: 10px;
  padding: 40px 15px;
}

h4.great {
  background: #00ac98;
  margin: 0 0 25px -60px;
  padding: 7px 15px;
  color: #ffffff;
  font-size: 18px;
  font-weight: 600;
  border-radius: 5px;
  display: inline-block;
  -webkit-box-shadow: 2px 4px 5px 0 #ccc;
  box-shadow: 2px 4px 5px 0 #ccc;
}

.price-slider {
  margin-bottom: 70px;
}

.form-pricing {
  background: #ffffff;
  padding: 20px;
  border-radius: 4px;
}

.price-form {
  background: #ffffff;
  margin-bottom: 10px;
  padding: 20px;
  border: 1px solid #eeeeee;
  min-height: 400px;
  border-radius: 4px;
}

.form-group {
  margin-bottom: 0;
}

.help-text {
  display: block;
  margin-top: -10px;
  margin-bottom: 10px;
  color: #737373;
  font-weight: 200;
  width: 188px;
}

.price-form label {
  font-weight: 200;
  font-size: 21px;
}

img.payment {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

hr.style {
  margin-top: 0;
  border: 0;
  border-bottom: 1px dashed #ccc;
  background: #999;
}
</style>
