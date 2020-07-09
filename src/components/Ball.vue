<template>
  <div class="loaders ball" :style="`top:${y}px;left:${x}px;`">
    <div class="loader">
      <div class="ball-scale-multiple" style="background: green;">
        <div :style="`background-color:${color};width:100px;height:100px;`"></div>
        <div :style="`background-color:${color};width:100px;height:100px;`"></div>
        <div :style="`background-color:${color};width:100px;height:100px;`"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Ball',
  props: {
    color: String,
      x: Number,
      y: Number
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.ball{
  position: fixed;
  width:50px;
}

</style>
