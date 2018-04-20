<template lang="html">
  <div class="btn-group">
    <el-button type="primary" @click="save">{{text[0]}}</el-button>
    <el-button type="primary" @click="submit">{{text[1]}}</el-button>
    <el-button @click="back">返回</el-button>
  </div>
</template>

<script>
export default {
  name: 'buttonGroup',
  props: {
    text: {
      type: Array
    }
  },
  methods: {
    save() {
      this.$emit('save')
    },
    submit() {
      this.$emit('submit')
    },
    back() {
      this.$emit('back')
    }
  }
}
</script>

<style lang="scss" scoped>
.btn-group {
  float: right;
}
</style>
