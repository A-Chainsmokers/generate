<template>
  <div class="drawer-main-class">
    <div class="drawer-child-class">
      <div class="child-text-class">
        <el-tag :type="!setting.sex ? 'info' :'success'" class="tag-class">
          性别
        </el-tag>
      </div>
      <div class="child-module-class">
        <el-radio-group v-model="setting.sex">
          <el-radio border v-for="(item,index) in options" :key="index" :label="item.value">{{ item.text }}</el-radio>
        </el-radio-group>
      </div>

    </div>

    <div class="drawer-child-class">
      <div class="child-text-class">
        <el-tag :type="!setting.birthday ? 'info' :'success'" class="tag-class">
          生日
        </el-tag>
      </div>
      <div class="child-module-class">
        <el-date-picker
            v-model="setting.birthday"
            type="date"
            format="yyyy 年 MM 月 dd 日"
            value-format="yyyy-MM-dd"
            placeholder="选择日期">
        </el-date-picker>
      </div>

    </div>

    <div class="drawer-child-class">
      <div class="child-text-class">
        <el-tag :type="setting.cityCode.length === 0 ? 'info' :'success'" class="tag-class">
          省市区
        </el-tag>
      </div>

      <div class="child-module-class">
        <el-cascader
            filterable
            clearable
            v-model="setting.cityCode"
            :options="cityOptions"
            :props="{value:'code',label:'name',children:'childList',checkStrictly : true}"
        ></el-cascader>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "drawer",
  props: {
    setting: {
      type: Object,
      default: () => {
      }
    },
    cityOptions: {
      type: Array,
      default: () => []
    },
    options: {
      type: Array,
      default: () => []
    }
  },
}
</script>

<style scoped lang="scss">

.drawer-main-class {
  padding: 10px;

  .drawer-child-class {
    margin: 20px auto;
    display: flex;
    width: 100%;
    justify-content: center;
    align-items: center;
    line-height: 40px;

    .child-text-class {
      text-align: right;
      min-width: 25%;

      .tag-class {
        text-align: center;
        width: 50%;
      }
    }

    .child-module-class {
      margin-left: 10px;
      min-width: 50%;
    }
  }
}
</style>
