<template>
  <div id="app">

    <me></me>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from 'vue'
import { useStore } from 'vuex'
import axios from '@/apis/index'
import Me from './components/Me'
interface ResData {
  user: {
    name: String
    child: {
      name: string
    }[]
  }[]
}
export default defineComponent({
  components:{Me},
  setup() {
    console.log('PROGRAM',PROGRAM)
    const store = useStore()
    const data = reactive({
      show: true,
    })
    if (process.env) {
      console.log(
        `VUEP_BASE_URL=${process.env.VUE_BASE_URL}`,
        process.env.BUILD_TIME
      )
    }
    setInterval(() => {
      store.dispatch('countUp')
    }, 1000)

    axios.get<ResData>('../static/head.json', {}).then((res) => {
      console.log(res)
    })
    return { ...toRefs(data) }
  },
})
</script>

<style scoped>
#app {
  color: #2c3e50;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
 }

</style>
