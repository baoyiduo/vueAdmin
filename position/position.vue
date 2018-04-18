<template lang="html">
  <div :class="{'small': size == 'small'}">
    <el-select :clearable="reset" :disabled="!provs.length" v-model="posData.province" @change="provsChange" placeholder="请选择省">
      <el-option
        v-for="item in provs"
        :key="item.id"
        :label="item.areaName"
        :value="item.id">
      </el-option>
    </el-select>
    <span v-if="!provs.length">初始化数据中<i class="el-icon-loading"></i></span>
    <el-select :clearable="reset" v-if="(needShow || cities.length) && level > 1" :disabled="!cities.length" v-model="posData.city" @change="citiesChange" placeholder="请选择市">
      <el-option
        v-for="item in cities"
        :key="item.id"
        :label="item.areaName"
        :value="item.id">
      </el-option>
    </el-select>
    <el-select :clearable="reset" v-if="(needShow ||  counties.length) && level > 2" :disabled="!counties.length" v-model="posData.district" @change="countiesChange" placeholder="请选择区">
      <el-option
        v-for="item in counties"
        :key="item.id"
        :label="item.areaName"
        :value="item.id">
      </el-option>
    </el-select>
    <el-select :clearable="reset" v-if="(needShow ||  streets.length) && level > 3" :disabled="!streets.length" v-model="posData.street" @change="streetsChange" placeholder="请选择街道">
      <el-option
        v-for="item in streets"
        :key="item.id"
        :label="item.areaName"
        :value="item.id">
      </el-option>
    </el-select>
  </div>
</template>

<script>
const keys = ['province', 'city', 'district', 'street']
export default {
  name: 'position',
  props: {
    value: [Object, String, Number],
    // 样式
    size: {
      type: String,
      default: 'normal'
    },
    // 是否展示禁用的项
    needShow: {
      type: Boolean,
      default: true
    },
    // 是否只输出最后一项的值
    lastValue: {
      type: Boolean,
      default: false
    },
    // 需要展示的层级
    level: {
      type: Number,
      default: 100
    },
    // 是否能够清空
    reset: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      posData: {
        province: '',
        city: '',
        district: '',
        street: ''
      },
      dataStatus: false
    }
  },
  created() {
    this.initData()
  },
  computed: {
    positionMapisRead() {
      return this.$store.state.positionMapisRead
    },
    provs() {
      if (this.dataStatus) {
        return window.positionMap.getData()[0] || []
      } else {
        return []
      }
    },
    cities() {
      let province = this.posData.province
      if (this.dataStatus && province) {
        return window.positionMap.getData()[province] || []
      } else {
        return []
      }
    },
    counties() {
      let city = this.posData.city
      if (this.dataStatus && city) {
        return window.positionMap.getData()[city] || []
      } else {
        return []
      }
    },
    streets() {
      let district = this.posData.district
      if (this.dataStatus && district) {
        return window.positionMap.getData()[district] || []
      } else {
        return []
      }
    }
  },
  watch: {
    positionMapisRead(v) {
      this.dataStatus = v
    }
  },
  methods: {
    /**
     * 初始化传入的数据 和 地理位置数据
     * @return {[type]} [description]
     */
    initData() {
      let value = this.value
      if (this.value.indexOf && this.value.indexOf(',') != -1) {
        let arr = value.split(',')
        value = {}
        arr.map((val, i) => {
          let k = keys[i]
          value[k] = +val
        })
      }
      this.posData = {
        ...this.posData,
        ...value
      }
      this.dataStatus = this.positionMapisRead
    },
    provsChange(val) {
      this.resetValue([
        'city',
        'district',
        'street'
      ])
      this.emitChange()
    },
    citiesChange() {
      this.resetValue([
        'district',
        'street'
      ])
      this.emitChange()
    },
    countiesChange() {
      this.resetValue([
        'street'
      ])
      this.emitChange()
    },
    streetsChange() {
      this.emitChange()
    },
    /**
     * 省份改变，其他数据要丢弃
     * @param {Array} [keys=[]] [description]
     */
    resetValue(keys = []) {
      keys.forEach(key => {
        this.posData[key] = ''
      })
    },
    emitChange() {
      let data = this.posData
      let level = this.level
      if (this.lastValue) {
        let emitData = ''
        keys.forEach((key, index) => {
          if (index < level) {
            emitData += data[key] + ','
          }
        })
        this.$emit('input', emitData.substr(0, emitData.length - 1))
      } else {
        this.$emit('input', this.posData)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.small {
    .el-select {
        width: 120px;
    }
}
</style>
