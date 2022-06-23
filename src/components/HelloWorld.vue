<template>
  <div class="hello" @click="emit_test">
    aaa
    <slot name="card"></slot>
    <slot name="card"></slot>
  </div>
</template>

<script>
export default {
  setup(props, context) {
    console.log(props, context)
    function emit_test() {
      context.emit('test')
    }
    return {
      emit_test
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
