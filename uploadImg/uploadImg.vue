<template lang="html">
  <div class="my-uploadImg">
    <el-upload
      class="upload-img"
      :class="{'upload-max': dataArr.length >= limit}"
      action="/api/pub/uploadImage"
      :on-preview="handlePreview"
      :on-remove="handleRemove"
      :before-remove="beforeRemove"
      :on-success="successUpload"
      multiple
      accept="image/*"
      :disabled="disabled"
      list-type="picture-card"
      :limit="+limit"
      :on-exceed="handleExceed"
      :file-list="fileList">
      <el-button size="small" type="primary" v-if="!disabled">点击上传</el-button>
      <div slot="tip" class="el-upload__tip"></div>
    </el-upload>
  </div>
</template>

<script>
export default {
  name: 'uploadImg',
  props: {
    limit: {
      type: [Number, String],
      default: 20
    },
    value: {
      type: [Array, String],
      required: true,
      default () {
        return []
      }
    },
    disabled: Boolean
  },
  data() {
    return {
      fileList: [],
      dataArr: [],
      currentImg: ''
    }
  },
  created() {
    this.initData(this.value)
  },
  watch: {
    value(data) {
      this.initData(data)
    }
  },
  methods: {
    initData(data = []) {
      let result = []
      if (typeof data === 'string') {
        if (data) {
          result = [{
            url: data
          }]
        }
      } else {
        result = data.map(url => {
          return {
            url
          }
        })
      }
      this.fileList = JSON.parse(JSON.stringify(result))
      this.dataArr = JSON.parse(JSON.stringify(result))
    },
    handleRemove(file, fileList = []) {
      this.dataArr = fileList.map(imgObj => {
        if (imgObj.status == 'success') {
          console.log(imgObj.status)
          if (imgObj.response) {
            return { url: imgObj.response.data }
          } else {
            return { url: imgObj.url }
          }
        }
      })
      this.emitChange()
    },
    successUpload(data, fileList) {
      if (data.errorCode == 0) {
        let url = data.data
        this.dataArr.push({ url })
      }
      this.emitChange()
    },
    handlePreview(file) {
      this.currentImg = file.url
      this.$alert(`<img width="100%" src=${file.url} alt="" />`, '', {
        dangerouslyUseHTMLString: true
      });
      console.log(file);
    },
    handleExceed(files, fileList) {
      this.$message.warning(`当前限制选择${this.limit} 个文件`);
    },
    beforeRemove(file, fileList) {
      return this.$confirm(`确定删除？`);
    },
    emitChange() {
      if (this.limit == 1) {
        this.$emit('input', this.dataArr[0].url)
      } else {
        this.$emit('input', this.dataArr.map(o => o.url))
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.upload-img {
    display: inline;
}
</style>
<style lang="scss" >
.my-uploadImg {
    .el-upload-list__item {
        width: 74px;
        height: 74px;
    }
    .el-upload--picture-card {
        width: 74px;
        height: 74px;
        line-height: 74px;
        border: none;
        background-color: transparent;
    }
    .el-upload-list.is-disabled + .el-upload--picture-card {
        display: none;
    }
    .upload-max {
        .el-upload {
            display: none;
        }
    }
}
</style>
