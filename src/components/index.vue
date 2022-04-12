<template>
  <div class="main-class">
    <div>
      <div>
        <el-alert
            class="alert-class"
            show-icon
            :title="!setting.sex ? '未选择性别' : `当前选择性别 : ${select(setting.sex,options).text}`"
            :type="!setting.sex ? 'warning' : 'success'" :closable="false">
        </el-alert>

        <el-alert
            class="alert-class"
            show-icon
            :title="setting.cityCode.length === 0 ? '未选择省市区' : `当前选择省市区 : ${cityName}`"
            :type="setting.cityCode.length === 0 ? 'warning' : 'success'" :closable="false">
        </el-alert>

        <el-alert
            class="alert-class"
            show-icon
            :title="!setting.birthday ? '未选择生日' : `当前选择生日 : ${setting.birthday}`"
            :type="!setting.birthday  ? 'warning' : 'success'" :closable="false">
        </el-alert>
      </div>

      <div class="input-groups-class">
        <div class="input-button-class">
          <el-input v-model="formData.username" disabled class="input-class">
            <template slot="prepend">姓名</template>
          </el-input>
          <el-button-group class="button-class">
            <el-button type="primary" icon="el-icon-document-copy" @click="copy(formData.username)"></el-button>
            <el-button type="primary" icon="el-icon-refresh" @click="refreshUserName()"></el-button>
            <el-button type="primary" icon="el-icon-info" @click="verifyRule(formData.username,'姓名')"></el-button>
          </el-button-group>
        </div>

        <div class="input-button-class">
          <el-input v-model="formData.card" disabled class="input-class">
            <template slot="prepend">身份证号</template>
          </el-input>
          <el-button-group class="button-class">
            <el-button type="primary" icon="el-icon-document-copy" @click="copy(formData.card)"></el-button>
            <el-button type="primary" icon="el-icon-refresh" @click="refreshId()"></el-button>
            <el-button type="primary" icon="el-icon-info" @click="verifyRule(formData.card,'身份证号')"></el-button>
          </el-button-group>
        </div>

        <div class="input-button-class">
          <el-input v-model="formData.phone" disabled class="input-class">
            <template slot="prepend">手机号</template>
          </el-input>
          <el-button-group class="button-class">
            <el-button type="primary" icon="el-icon-document-copy" @click="copy(formData.phone)"></el-button>
            <el-button type="primary" icon="el-icon-refresh" @click="refreshTel()"></el-button>
            <el-button type="primary" icon="el-icon-info" @click="verifyRule(formData.phone,'手机号')"></el-button>
          </el-button-group>
        </div>

        <div class="input-button-class">
          <el-input v-model="formData.email" disabled class="input-class">
            <template slot="prepend">邮箱</template>
          </el-input>
          <el-button-group class="button-class">
            <el-button type="primary" icon="el-icon-document-copy" @click="copy(formData.email)"></el-button>
            <el-button type="primary" icon="el-icon-refresh" @click="refreshEmail()"></el-button>
            <el-button type="primary" icon="el-icon-info" @click="verifyRule(formData.email,'邮箱')"></el-button>
          </el-button-group>
        </div>

        <div class="input-button-class">
          <el-input v-model="formData.road" disabled class="input-class">
            <template slot="prepend">地址</template>
          </el-input>
          <el-button-group class="button-class">
            <el-button type="primary" icon="el-icon-document-copy" @click="copy(formData.road)"></el-button>
            <el-button type="primary" icon="el-icon-refresh" @click="refreshRoad()"></el-button>
            <el-button type="primary" icon="el-icon-info" @click="verifyRule(formData.road,'地址')"></el-button>
          </el-button-group>
        </div>
      </div>

    </div>

    <div>
      <el-button type="warning" icon="el-icon-refresh-right" plain @click="_initData()">重置</el-button>
      <el-button type="primary" icon="el-icon-setting" plain @click="show = true">选项</el-button>
    </div>


    <el-drawer
        title="选项"
        size="40%"
        :visible.sync="show"
        direction="rtl">

      <div class="drawer-content-class">
        <drawer :setting="setting" :city-options="cityOptions" :options="options"></drawer>

        <div class="button-groups-class">
          <div class="button-item-class">
            <el-button type="warning" class="button-class" icon="el-icon-refresh-right" @click="resetSetting()"
                       plain>重置
            </el-button>
          </div>

          <div class="button-item-class">
            <el-button type="primary" class="button-class" icon="el-icon-magic-stick" plain @click="send()">
              确定
            </el-button>
          </div>

        </div>
      </div>
    </el-drawer>

    <el-dialog title="正则校验"
               :visible.sync="dialogVisible"
               width="50%">
      <div>
        <el-form :model="verifyForm" :rules="rules" ref="verifyForm" status-icon label-width="100px">
          <el-form-item :label="verifyForm.label" prop="val">
            <el-input v-model="verifyForm.val" disabled></el-input>
          </el-form-item>
          <el-form-item label="正则" prop="rule">
            <el-input type="textarea" :autosize="{ minRows: 5, maxRows: 10 }" v-model="verifyForm.rule"></el-input>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="submitForm('verifyForm')">校验</el-button>
          </el-form-item>

        </el-form>
      </div>
    </el-dialog>

  </div>
</template>

<script>
import Drawer from '@/components/drawer'


import {init, getChineseName, getTel, getQQEmail, generateID, getRoad} from '@/utils/generate'
import {cityCodeList} from "@/utils/city";

export default {
  name: 'index',
  components: {
    'drawer': Drawer
  },
  data() {
    const validateValue = (rule, value, callback) => {
      if (!this.verifyForm.rule) {
        return callback(new Error('正则不能为空'));
      }
      let reg = new RegExp(this.verifyForm.rule)

      if (!reg.test(value)) {
        return callback(new Error('正则校验失败!'));
      } else {
        callback();
      }
    };
    return {
      formData: {},
      show: false,
      setting: {
        sex: undefined,
        birthday: undefined,
        cityCode: [],
      },
      cityOptions: cityCodeList,
      options: [{
        text: '男', value: 1
      }, {
        text: '女', value: 2
      }],
      dialogVisible: false,
      verifyForm: {
        val: undefined,
        rule: undefined,
        label: undefined
      },
      rules: {
        val: [
          {validator: validateValue, trigger: 'blur'}
        ],
        rule: [
          {required: true, message: '请输入正则!', trigger: 'blur'}
        ],
      }
    }
  },
  computed: {
    cityName() {
      let name;
      let [p, c, d] = this.setting.cityCode;

      if (p) {
        let province = this.cityOptions.find(item => item.code == p)
        name = province.name

        if (c) {
          let city = province.childList.find(item => item.code == c)
          name += "-" + city.name

          if (d) {
            let district = city.childList.find(item => item.code == d)
            name += "-" + district.name
          }
        }

      }
      return name
    }
  },
  mounted() {
    this._initData()
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.msgSuccess("校验成功!");
        } else {
          this.msgError("校验失败!");
          return false;
        }
      });
    },

    verifyRule(val, label) {
      this.verifyForm = {
        val,
        label,
        rule: undefined
      }
      this.dialogVisible = true;
    },
    _initData() {
      let _setting = {
        ...this.setting
      }
      _setting.cityName = this.cityName

      this.formData = init(_setting)
    },
    send() {
      this._initData();
      this.show = false
    },

    copy(value) {
      // createElement() 方法通过指定名称创建一个元素
      let copyInput = document.createElement("input");
      //val是要复制的内容
      copyInput.setAttribute("value", value);
      document.body.appendChild(copyInput);
      copyInput.select();
      try {
        let copied = document.execCommand("copy");
        if (copied) {
          document.body.removeChild(copyInput);
          this.msgSuccess("复制成功");
        }
      } catch {
        this.msgError("复制失败，请检查浏览器兼容");
      }
    },
    refreshUserName() {
      let {sex} = this.setting
      this.formData.username = getChineseName(sex)
    },
    refreshTel() {
      this.formData.phone = getTel()
    },
    refreshEmail() {
      this.formData.email = getQQEmail()
    },
    refreshId() {
      let {sex, birthday, cityCode} = this.setting

      this.formData.card = generateID(birthday, cityCode, sex)
    },

    refreshRoad() {
      this.formData.road = getRoad(this.cityName)
    },

    select(key, array) {
      let result = array.find(item => item.value === key)
      if (result) return result
      return key
    },
    resetSetting() {
      this.setting = {
        sex: undefined,
        cityCode: [],
        birthday: undefined
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.main-class {
  margin: 20px auto;

  .alert-class {
    margin: 10px auto;
    width: 90%;
  }

  .input-groups-class {
    .input-button-class {
      margin: 10px auto;
      line-height: 40px;

      .input-class {
        width: 50%;
        margin: 10px auto;
      }

      .button-class {
        margin-left: 1%;
      }
    }
  }

  .drawer-content-class {

    .button-groups-class {
      position: absolute;
      bottom: 20px;
      width: 100%;

      .button-item-class {
        width: 100%;

        .button-class {
          margin: 20px;
          align-items: center;
          justify-content: center;
          display: flex;
          flex: 1 0 auto;
          min-width: 90% !important;
        }
      }
    }
  }


}


/deep/ .el-input-group__prepend {
  width: 20%;
}

/deep/ .el-input.is-disabled .el-input__inner {
  color: #606266;
  background-color: #FFF;
}

</style>
