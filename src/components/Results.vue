<template>
    <div class="result">
        <p class="text-green text-subtitle2 text-capitalize">DESGLOSE MENSUAL</p>
        <div v-for="month in data" class="row items-center" v-bind:key="month.fecha">
          <p class="col-1 text-subtitle1 text-weight-medium q-mx-md text-right">{{ month.dato | formatNumber }}</p>
          <p class="col-9 text-weight-light text-capitalize">{{ month.fecha }}</p>
        </div>
    </div>
</template>
<script>
export default {
  name: 'Results',
  props: ['data'],
  filters: {
    formatNumber: function (value) {
      if (!value) return ''
      return Math.round(value * 100) / 100 + '%'
    }
  }
}
</script>
