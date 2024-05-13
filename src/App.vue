<template>
  <div>
    <div class="code">{{ code }}</div>
    <CodeEdtior :value.sync="code" :height="800" :readOnly="true"></CodeEdtior>
  </div>
</template>
<script>
import CodeEdtior from './components/CodeEdtior.vue'

export default {
  components: { CodeEdtior },
  data(){
    return{
      code:`companies:
- id: 1
  name: company1
  price: 200W
- id: 2
  name: company2
  price: 500W`
    }
  }
}
</script>
<style scoped>
.code {
  background: black;
  color: #fff;
  padding: 10px;
}
</style>