<template>
  <div class="user-information-modify">
    <ul style=""> 
      <li>
        <label for="">{{$t('tableKey.name')}}：</label>
        <span>{{userInfo.name}}</span>
      </li>
      <li v-for="(info, infoKey) in infoConfig" :key="infoKey">
        <label for="">{{$t(info.label)}}：</label>
        <input v-if="activeKey === info.key" @blur="saveInfo(info.key)" v-model="userInfo[info.key]" type="text" class="form-control model-input">
        <span v-else>{{userInfo[info.key]}} <i @click="activeKey = info.key" class="fa fa-pencil-square-o" aria-hidden="true"></i></span>
      </li>
      <!-- <li>
        <label for="">{{$t('tableKey.password')}}：</label>
        <template v-if="activeKey === 'new_password'">
          <input v-model="userInfo.new_password" type="text" class="form-control model-input">
          <input v-model="userInfo.re_new_password" type="text" class="form-control model-input">
          <button type="button" class="btn-confirm-f">{{$t('button.confirm')}}</button>
        </template>
        <span v-else>{{userInfo.new_password}} <i @click="activeKey = 'new_password'" class="fa fa-pencil-square-o" aria-hidden="true"></i></span>
      </li> -->
      <template v-if="activeKey === 'new_password'">
        <li>
          <label for="">{{$t('button.password')}}：</label>
          <input v-model="userInfo.new_password" type="text" class="form-control model-input">
        </li>
        <li>
          <label for="">{{$t('button.rePassword')}}：</label>
          <input v-model="userInfo.re_new_password" type="text" class="form-control model-input">
          <button type="button" class="btn-confirm-f" @click="confirmPassword">{{$t('button.rePassword')}}</button>
          <button type="button" class="btn-cancle-f" @click="abandonModify">{{$t('button.cancle')}}</button>
        </li> 
      </template>
      <li v-else>
        <label for="">{{$t('button.password')}}：</label>
        <span>{{userInfo.new_password}} <i @click="activeKey = 'new_password';userInfo.new_password=''" class="fa fa-pencil-square-o" aria-hidden="true"></i></span>
      </li>
      <li>
        <label for="">{{$t('tableKey.activeDate')}}：</label>
        <span>{{userInfo.created_string}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: '',
  data() {
    return {
      infoConfig: [
        { label: 'tableKey.nickname', key: 'display_name' },
        { label: 'tableKey.email', key: 'email' },
        { label: 'tableKey.phone', key: 'phone' },
        // { label: 'tableKey.password', key: 'new_password' },
      ],
      activeKey: null,
      userInfo: {}
    }
  },
  mounted () {
    this.userInformation()
  },
  methods: {
    userInformation () {
      this.$root.$httpRequestEntrance.httpRequestEntrance('GET', this.$root.apiCenter.setup.userInformation.get, {}, (responseData) => {
        this.userInfo = responseData
        this.userInfo.new_password = '**********'
        this.userInfo.re_new_password = ''
      })
    },
    saveInfo (key) {
      this.activeKey = null
      let params = null
      if (key === 'new_password') {
        let Base64 = require('js-base64').Base64
        // eslint-disable-next-line no-const-assign
        params = {
          [key]: Base64.encode(this.userInfo[key]),
          're_new_password': Base64.encode(this.userInfo[key])
        }
      } else {
        // eslint-disable-next-line no-const-assign
        params = {
          [key]: this.userInfo[key]
        }
      }
      this.userInfo.new_password = '**********'
      this.$root.$httpRequestEntrance.httpRequestEntrance('POST', this.$root.apiCenter.setup.userInformation.update, params, () => {
        this.$Message.success(this.$t('tips.success'))
        this.userInformation()
      })
    },
    confirmPassword () {
      if (this.userInfo.new_password.trim() === this.userInfo.re_new_password.trim()) {
        this.saveInfo('new_password')
      } else {
        this.$Message.success(this.$t('tips.failed'))
      }
    },
    abandonModify () {
      this.activeKey = ''
      this.userInfo.re_new_password = ''
      this.userInfo.new_password = '******'
    }
  },
  components: {},
}
</script>

<style scoped lang="less">
.user-information-modify {
  min-width:500px;
  font-size: 15px;
  font-weight: 400;
  margin-top:20px;
  label {
    width:150px;
    text-align: right;
    margin-bottom: 0;
  }
  li {
    padding: 8px;
    i {
      margin-left: 20px;
      color: @color-blue;
    }
  }
}
.form-control {
  width: 15%;
}
.form-control:focus {
  box-shadow: none;
}
.model-input{
  font-size: 12px;
  display: inline-block;
}
</style>
