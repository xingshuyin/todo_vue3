<template>



  <Suspense>
    <template v-slot:default>
      <HelloWorld msg="suspense">

      </HelloWorld>
    </template>
    <template v-slot:fallback>
      loading
    </template>
  </Suspense>
  <HelloWorld msg="Welcome to Your Vue.js App" @test="show">
  </HelloWorld>
  <span>{{ name }} {{ sex }} {{ age }}</span>
  <button @click="show"></button>
  <button @click="fun"></button>
</template>

<script>

// import HelloWorld from './components/HelloWorld.vue'
const HelloWorld = defineAsyncComponent(() => import('./components/HelloWorld.vue'))  // 异步加载组件
import {
  ref, reactive, defineAsyncComponent
} from 'vue'
export default {
  name: 'App',
  props: [],
  setup(props, context) { //数据和方法都写在这, 在beforecreate之前执行
    console.log(props, context)
    //普通变量
    let name = ref('xsy');  //refimpl对象  引用实现对象(引用对象)  reference implement
    let sex = ref('男');
    let age = ref(12);

    //对象属性-ref
    let message1 = ref({ // ref创建对象
      d1: 1,
      d2: 2
    });

    //对象属性-reactive
    let message2 = reactive({ // reactive创建对象
      d1: 1,  //访问属性不需要加value
      d2: 2,
      d3: {
        d4: 1
      }
    })
    let message3 = reactive(['a', 'b'])  // reavtive创建列表对象
    return {
      name, sex, age, message1, message2, message3
    }
  },
  components: {
    HelloWorld,

  },
  //生命周期
  beforeCreate() { },
  created() { },
  beforeMount() { },
  mounted() { },
  beforeUpdate() { },
  updated() { },
  beforeUnmount() { },
  unmounted() { },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

button {
  width: 20px;
  height: 10px;
}
</style>
