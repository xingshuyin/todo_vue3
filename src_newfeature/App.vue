<template>
  <HelloWorld msg="Welcome to Your Vue.js App" @test="show">
    <template v-slot:card>
      <div class="card">
        {{ fullMessage1 }}
        {{ point.x }}
        {{ point.y }}
        d2: {{ d2 }}
      </div>
    </template>
  </HelloWorld>
  <span>{{ name }} {{ sex }} {{ age }}</span>
  <button @click="show"></button>
  <button @click="fun"></button>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import usePoint from './hooks/usePoint'
import {
  ref, reactive, computed, watch, watchEffect, onMounted,
  toRef, toRefs, shallowRef, readonly, shallowReadonly,
  toRaw, markRaw, customRef, provide
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
    //监视
    watch(name, (newValue, oldValue) => {  //监视普通属性不需要加.value; 但是监视ref对象需要加.value
      console.log(newValue, oldValue)
    })
    // 监视多个值
    watch([age, sex], (newValue, oldValue) => {
      oldValue  // [oldname, oldsex]
      newValue // [newname,newsex]
      console.log(newValue, oldValue)
    }, { immediate: true })  //渲染完执行一次
    //计算属性
    let fullMessage1 = computed(function () { return name.value + sex.value + age.value })
    let fullMessage2 = computed({
      get() {
        return name.value + sex.value + age.value
      },
      set(value) {
        return value
      }
    })

    //对象属性-ref
    let message1 = ref({ // ref创建对象
      d1: 1,
      d2: 2
    });

    const message4 = shallowRef({  // 浅层监视(第一层)
      d5: 1,
      d6: 3
    })


    let message_myref = myRef(message1)

    //对象属性-reactive
    let message2 = reactive({ // reactive创建对象
      d1: 1,  //访问属性不需要加value
      d2: 2,
      d3: {
        d4: 1
      }
    })
    let message3 = reactive(['a', 'b'])  // reavtive创建列表对象
    const message2Readonly = readonly(message2) //不能改
    const message2ShallowReadonly = shallowReadonly(message2)  //第一层不能改
    //监视ref 对象
    watch(message1.value, (newValue, oldValue) => {  //本质上是监视 reactive的proxy对象
      console.log(newValue, oldValue)
    })
    //监视ref 对象
    watch(message1, (newValue, oldValue) => {
      console.log(newValue, oldValue)
    }, { deep: true }) // deep开启深度监视


    //自定义ref
    function myRef(value) {
      return customRef(
        (track, trigger) => {
          let timer
          return {
            get() {
              track()  //追踪value值的改变
              return value
            },
            set(newValue) {
              clearTimeout(timer)
              timer = setTimeout(() => { //输入防抖
                value = newValue
                trigger()  //触发重新渲染              
              }, 500)

            }
          }
        }
      )

    }

    //监视对象; 自动深度监视, oldvalue是newvalue
    watch(message2, (newValue, oldValue) => {
      console.log(newValue, oldValue)
    })
    //监视数值类型对象子属性,监视用函数返回属性值, 监视的是值所以 oldvalue不是newvalue
    watch(() => message2.d1, (newValue, oldValue) => {
      console.log(newValue, oldValue)
    })
    //监视对象类型的对象子属性,监视用函数返回属性值,需要{deep:true}开启深度监视, oldvalue是newvalue
    watch(() => message2.d3, (newValue, oldValue) => {
      console.log(newValue, oldValue)
    }, { deep: true })

    watchEffect(() => {  //里面用到谁就监视谁, 初始化时也执行
      console.log(name)
    })
    //读取响应式对象的数据为普通对象, 修改它不会修改原响应式对象
    message2 = toRaw(message1)
    //跳过响应式,响应式转普通对象,使其永远不能变为响应式
    const message2d3 = markRaw(message2.d3)  //当属性为第三方类库的时候使用这个能大大提高效率,跳过响应式

    //方法
    function show() {  //不要使用async
      alert('show')
    }
    function fun() {
      age.value++  //修改引用对象
      message1.value.d1++  //修改对象的值
      message2.d1++  // reactive  对象直接修改值
      message3[1] = 'd'
    }

    // //生命周期-setup
    // onBeforeMount(() => {
    //   console.log('onbeforemount')
    // })
    onMounted(() => {
      console.log('onMounted')
    })
    let point = usePoint()  //使用外部钩子


    let d2 = toRef(message2, 'd2')  //单独取出对象的一个属性, 变化会变动源数据
    let d4 = toRef(message2.d3, 'd4')
    let messageRefs = toRefs(message2)  //把所有属性变成toref的结果,然后可以直接使用属性

    provide('message', message2)  //类似事件总线,在他的后代组件中可以直接通过, inject('message')  获取数据
    return {
      name, sex, age, show, fun, fullMessage1,
      fullMessage2, point, d2, d4, message4,
      message2Readonly, message2ShallowReadonly,
      message2d3, message_myref,
      ...messageRefs
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
