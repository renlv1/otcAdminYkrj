<template>
  <div class="c-page mar-right">
    <div class="crumb">
      <span>用户是否在线</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model="valueID" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label"></div>
          <div class="input-wrap"></div>
        </div>
        <div class="f1"></div>
        <div class="btns">
          <div @click="resetFields()" class="btn black">清空</div>
          <div class="btn" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="list" :total="total" @change="getData">
        <div class="web-table">
          <table class="center">
            <tr class="thead">
              <td>用户名</td>
              <td>设备号</td>
              <td>设备类型</td>
            </tr>
            <tr v-for="(item, index) in isListNull(list)" :key="index" class="tbody-tr">
              <td>{{item.userName}}</td>
              <td>{{item.deviceNumber}}</td>
              <td>{{item.deviceType}}</td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
    </div>
  </div>
</template>

<script>
  // import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        valueID: '',// 用户名
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        allList: [],
        pageSize: 10,
      }
    },
    created() {
      // this.getData()
    },
    methods: {
      resetFields() {
        this.valueID = ''
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/user/userOnLine/queryUserOnLineList', {
          userName: this.valueID, // 用户名
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize,
        }).then((res) => {
          this.loadShow = false
          this.list = res.data.Rows
          this.total = res.data.Total
        }).catch(err => {
          this.loadShow = false
          console.log(err)
        })
      },
      getTurn(page) {
        this.list = this.allList.slice((page -1)  * this.pageSize, page * this.pageSize)
      },
      search() {
        this.total = 1 // hook listWrap组件的loading
        this.getData()
      },
    },
    
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .problem-box{
    margin-top 20px
    img{
      margin-right 10px
    }
    label{
      font-size 16px
      color #121212
    }
  }
  .blue
    color #2176ff
  .wrap
    background-color: @white;
  .search
    position relative
    display flex
    flex-direction row
    flex-wrap wrap
    .search-box
      margin-top 30px
      margin-bottom 0
      display flex
      flex-direction row
      align-items center
      .dropdown-wrap
        cursor pointer
        text-align center
        line-height 40px
        width: 180px;
        margin-right: 20px;
        background-color #fff
        border: 1px solid #DCDFE6;
        border-radius: 6px;
        padding: 0 10px;
        font-size 15px
        height: 40px;
    .btns
      margin-top 30px
      margin-right 50px
      .btn
        cursor pointer
</style>

<style scoped>
  >>>.freezeListWrap .empty {
    top: 20px;
  }
  
  /*>>>.el-date-editor.el-input, .el-date-editor.el-input__inner {*/
  /*padding: 0 10px !important;*/
  /*margin-right: 20px !important;*/
  /*width: 130px !important;*/
  /*height: 25px;*/
  /*}*/
  
  >>> .el-dialog {
    margin: 0 auto;
    width: 80%;
    box-shadow: none;
  }
  
  >>> .el-dialog .el-dialog__headerbtn {
    top: 20px;
    right: 15px;
  }
  
  /*>>> .el-dialog .el-dialog__headerbtn .el-icon-close:before {*/
  /*  border: 2px solid #ccc;*/
  /*  border-radius: 100%;*/
  /*}*/
  
  /*>>> .el-date-editor .el-input__inner {*/
  /*padding: 0 10px !important;*/
  /*}*/
  
  textarea {
    font-size: 14px;
    padding: 16px 0 0 18px;
    color: #000;
  }
  
  textarea::-webkit-input-placeholder {
    color: #b3b3b3;
  }
  
  .input:-moz-placeholder {
    color: #b3b3b3;
  }
  
  .input:-ms-input-placeholder {
    color: #b3b3b3;
  }
</style>
