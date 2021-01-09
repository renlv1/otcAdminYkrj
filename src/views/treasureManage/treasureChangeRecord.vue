<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>赏金单分配记录</span>
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
          <div class="label">藏宝图ID：</div>
          <div class="input-wrap"><input v-model="sourceId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">支付ID：</div>
          <div class="input-wrap"><input v-model="payId" type="number"></div>
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
              <td>挖宝人名</td>
              <td width="15%">挖宝人地址</td>
              <td>更换费用</td>
              <td>藏宝图ID</td>
              <td>旧任务ID</td>
              <td>新任务ID</td>
              <td>旧任务来源</td>
              <td>新任务来源</td>
              <td>支付ID</td>
              <td>换任务时间</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{item.total}}</td>
              <td>{{item.mapId}}</td>
              <td>{{item.oldTaskId}}</td>
              <td>{{item.newTaskId}}</td>
              <td>{{oldSourceFn(item.oldSource)}}</td>
              <td>{{newSourceFn(item.newSource)}}</td>
              <td>{{item.payId}}</td>
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
        payId: '',
        id: '',
        userName: '',//登录账户名
        sourceId: '',
        accountAddress: '',//登录位置IP
        startTime: '',
        endTime: '',
        isArr:[
          {text: '全部',id: ''},
          {text: '是',id: 1},
          {text: '否',id: 2},
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
      // this.getData()
    },
    methods: {
      oldSourceFn(index){
        if(index === 1) return '玩家'
        if(index === 2) return '系统'
      },
      newSourceFn(index){
        if(index === 1) return '玩家'
        if(index === 2) return '系统'
      },
      resetFields() {
        this.id = ''
        this.userName = ''
        this.sourceId = ''
        this.payId = ''
        this.accountAddress = ''
        this.clientType = ''
        this.startTime = ''
        this.endTime = ''
        this.isText = ''
        this.equipment = ''
      },
      handleCommand2(command) {
        this.isText = command.text
        this.deviceFlag = command.id
      },
      //加速状态
      handleCommand(command) {
        this.equipment = command.text
        this.clientType = command.id
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/map/change/queryMapChangeList', {
          userName: this.userName, //
          id: this.id, //
          mapId: this.sourceId,
          payId: this.payId,
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
