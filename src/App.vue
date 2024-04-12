
<template>
  <div class="main">
    <FormLoadFile title="Label" hint="Hint text" :active= "true" @submit="handleData"/>
  </div>
</template>

<script setup lang="ts">
import FormLoadFile from './components/FormLoadFile.vue'

const handleData = (data:void) => {
  console.log('Received data:', data);
};

</script>


<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@100..900&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Inter;
}
.main {
  display: flex;
  justify-content: center;
  margin-top: 36px;
}
</style>


