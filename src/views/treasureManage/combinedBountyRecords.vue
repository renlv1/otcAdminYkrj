<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>赏金单合并记录</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="id" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">挖宝人名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">失效宝图单ID：</div>
          <div class="input-wrap"><input v-model="sourceId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">新的宝图单ID：</div>
          <div class="input-wrap"><input v-model="newMapId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">合并类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="handleCommand">
              <span class="el-dropdown-link">
                {{ equipment || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in statusArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="f1"></div>
        <div class="btns">
          <div @click="resetFields()" class="btn black">清空</div>
          <div class="btn" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="isListNull(list)" :total="total" @change="getData">
        <div class="web-table">
          <table class="center">
            <tr class="thead">
              <td width="6%">ID</td>
              <td>用户名</td>
              <td width="15%">用户地址</td>
              <td>失效宝图单ID</td>
              <td>失效宝图单金额</td>
              <td>新宝图单ID</td>
              <td>新宝图单金额</td>
              <td>合并后宝图单金额</td>
              <td>合并类型</td>
              <td>手续费</td>
              <td>说明</td>
              <td>创建时间</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{item.oldMapId}}</td>
              <td>{{item.oldMapAmount}}</td>
              <td>{{item.newMapId}}</td>
              <td>{{item.newMapAmount}}</td>
              <td>{{item.mergeAfterAmount}}</td>
              <td>{{mergeTypeFn(item.mergeType)}}</td>
              <td>{{item.mergeFee}}</td>
              <td>{{isEmptyText(item.remark)}}</td>
              <td v-html="timeStrDate(item.createAt)"></td>
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
  export default {
    data() {
      return {
        id: '',
        userName: '',//登录账户名
        sourceId: '',
        accountAddress: '',//登录位置IP
        newMapId: '',
        startTime: '',
        endTime: '',
        pickerOptionsStart: {
          disabledDate: time => {
            if (this.endTime) {
              return time.getTime() > new Date(this.endTime).getTime()
            }
          }
        },
        pickerOptionsEnd: {
          disabledDate: time => {
            if (this.startTime) {
              return time.getTime() < new Date(this.startTime).getTime() - 86400000
            }
          }
        },
        isArr:[
          {text: '全部',id: ''},
          {text: '正常打赏',id: 1},
          {text: '博格基金转换',id: 2},
        ], //首次登录此设备
        isText: '',
        status: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        statusArr: [],//设备类型
        deviceFlag: '', //是否第一次登陆此设备
        clientType: '',//  设备类型
        equipment: '',
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      mergeTypeFn(index){
        return this.statusArr[index].text
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=map.MapMergeRecord$TypeEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.TypeEnum
            // this.isArr = res.data.data.BountyTypeEnum
          }
        })
      },
      resetFields() {
        this.newMapId = ''
        this.id = ''
        this.userName = ''
        this.sourceId = ''
        this.accountAddress = ''
        this.clientType = ''
        this.startTime = ''
        this.endTime = ''
        this.isText = ''
        this.equipment = ''
      },
      //合并类型
      handleCommand(command) {
        this.equipment = command.text
        this.clientType = command.id
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/map/mapMerge/queryMapMergeList', {
          userName: this.userName, //
          id: this.id, //
          oldMapId: this.sourceId,
          newMapId: this.newMapId,
          mergeType: this.clientType === 'selected' ? this.clientType = '' : this.clientType, //合并类型
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          this.list = res.data.page.list
          this.total = res.data.page.totalCount
        }).catch(err => {
          this.loadShow = false
          console.log(err)
        })
      },
      search() {
        this.total = 1 // hook listWrap组件的loading
        this.getData()
      },
    },
    
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
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
  
  >>> .el-dialog .el-dialog__headerbtn .el-icon-close:before {
    border: 2px solid #ccc;
    border-radius: 100%;
  }
  
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
