<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <ul>
      <li v-for="(item,index) in arr" :key="index">{{item.title}}：{{item.text}}</li>
    </ul>
  </div>
</template>

<script>
import {
  product
} from '@/api/index/index'
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      arr:[]
    }
  },
  mounted () {
    product().then(res=>{
      console.log(res)
      this.arr = res.data
      console.log(this.arr)
    }).catch(error => {
      console.log(error);
    });
  },
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
