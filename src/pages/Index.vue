<template>
  <q-page class="flex flex-center">
    <q-card class="main-card q-my-xl">
      <q-card-section class="main-card-header text-white q-pa-sm">
        <div class="text-h5 text-weight-medium text-capitalize q-pl-sm">CALCULADORA DE INFLACIÓN</div>
      </q-card-section>
      <q-separator />
      <q-card-section>
        <div class="row q-px-sm" style="width: 55%;">
          <div class="col-12">
            <p class="text-green text-subtitle2 text-capitalize">DESDE</p>
          </div>
          <div class="col-6 q-pr-sm">
            <q-select outlined v-model="startMonth" :options="months" dense options-dense @input="emptyData"></q-select>
            <p class="q-pl-md q-pt-xs text-gray-1">MES</p>
          </div>
          <div class="col-6">
            <q-select outlined v-model="startYear" :options="years" dense options-dense @input="emptyData"></q-select>
            <p class="q-pl-md q-pt-xs text-gray-1">AÑO</p>
          </div>
          <div class="col-12">
            <p class="text-green text-subtitle2 text-capitalize">HASTA</p>
          </div>
          <div class="col-6 q-pr-sm">
            <q-select outlined v-model="endMonth" :options="months" dense options-dense @input="emptyData"></q-select>
            <p class="q-pl-md q-pt-xs text-gray-1">MES</p>
          </div>
          <div class="col-6">
            <q-select outlined v-model="endYear" :options="years" dense options-dense @input="emptyData"></q-select>
            <p class="q-pl-md q-pt-xs text-gray-1">AÑO</p>
          </div>
        </div>
        <q-btn unelevated class="no-border-radius q-mb-lg q-mt-md" color="orange" label="CALCULAR" style="min-width: 215px;" @click="fetchData" />
        <div v-if="data">
          <p class="text-green text-subtitle2 text-capitalize">RESULTADO</p>
          <div class="flex no-wrap items-center">
            <p class="text-h5 text-weight-medium q-mx-md">{{ total | formatNumber }}</p>
            <p class="text-weight-light text-capitalize text-justify" style="max-width: 55%;">
              INFLACIÓN DE <span class="text-weight-bold">{{ startMonth.label }} {{ startYear }}</span> a
              <span class="text-weight-bold">{{ endMonth.label }} {{ endYear }}</span> SEGÚN EL INDICE
              NACIONAL DE PRECIOS AL CONSUMIDOR (IPC)
            </p>
          </div>
          <Results v-bind:data="data"></Results>
        </div>
        <div v-else>
          <div v-if="newValues">
            <p class="q-py-md text-gray-1">SELECCIONANDO NUEVOS VALORES</p>
          </div>
          <div v-else>
            <p class="q-py-md text-gray-1">NO SE ENCONTRARÓN RESULTADOS</p>
          </div>
        </div>
      </q-card-section>
    </q-card>
  </q-page>
</template>
<script>
const axios = require('axios')

import Results from '../components/Results'

export default {
  name: 'PageIndex',
  data () {
    return {
      months: [
        { label: 'ENERO', value: '01' },
        { label: 'FEBRERO', value: '02' },
        { label: 'MARZO', value: '03' },
        { label: 'ABRIL', value: '04' },
        { label: 'MAYO', value: '05' },
        { label: 'JUNIO', value: '06' },
        { label: 'JULIO', value: '07' },
        { label: 'AGOSTO', value: '08' },
        { label: 'SEPTIEMBRE', value: '09' },
        { label: 'OCTUBRE', value: '10' },
        { label: 'NOVIEMBRE', value: '11' },
        { label: 'DICIEMBRE', value: '12' }
      ],
      startMonth: { label: 'ENERO', value: '01' },
      startYear: null,
      endMonth: null,
      endYear: null,
      data: null,
      newValues: false
    }
  },
  computed: {
    years () {
      return Array.from({ length: new Date().getFullYear() - 1968 }, (value, index) => 1969 + index)
    },
    total () {
      if (!this.data) return null
      var totals = this.data.map(e => parseFloat(e.dato))
      return totals.reduce((a, b) => a + b) / totals.length
    }
  },
  methods: {
    fetchData () {
      const PROXY_URL = 'https://cors-anywhere.herokuapp.com/'
      const URL = `https://www.banxico.org.mx/SieAPIRest/service/v1/series/SP30577/datos/${this.startYear}-${this.startMonth.value}-01/${this.endYear}-${this.endMonth.value}-01`

      var vm = this

      axios.get(PROXY_URL + URL, {
        headers: {
          'Bmx-Token': 'c6cf25fdc4ad4612531310668858a6bc83b4857181ec759c23e363f578c010d3'
        }
      })
        .then(function (response) {
          vm.data = response['data']['bmx']['series']['0']['datos']
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    emptyData () {
      this.newValues = true
      this.data = null
    }
  },
  filters: {
    formatNumber: function (value) {
      if (!value) return ''
      return Math.round(value * 100) / 100 + '%'
    }
  },
  created () {
    var date = new Date()

    // Get current dates
    this.endMonth = this.months[date.getMonth()]
    this.startYear = date.getFullYear()
    this.endYear = date.getFullYear()

    // Get data
    this.fetchData()
  },
  components: {
    Results
  }
}

</script>
