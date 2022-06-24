<template>
  <div class="hello" @click="emit_test">
    aaa
    <slot name="card"></slot>
    <slot name="card"></slot>
  </div>
  <button @click="show">show</button>
  <teleport to="body">
    <div v-show="isshow">
      弹窗
      <button @click="close">close</button>
    </div>
  </teleport>
</template>

<script>
import { inject, ref } from 'vue'
export default {
  setup(props, context) {
    console.log(props, context)
    function emit_test() {
      context.emit('test')
    }
    let message = inject('message')  //获取祖先组件provide的数据
    let isshow = ref(false)
    function show() {
      isshow.value = true
    }
    function close() {
      isshow.value = false
    }
    return {
      emit_test, message, isshow, show, close
    }
  },
  name: 'HelloWorld',
  props: { //自定义属性中不放在这的会被放在context的attrs中
    msg: String
  },
  emits: ['test']
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
