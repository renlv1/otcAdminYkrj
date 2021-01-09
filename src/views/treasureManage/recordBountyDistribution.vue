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
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">数据源ID：</div>
          <div class="input-wrap"><input v-model="sourceId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">加速状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{equipment || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in statusArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{isText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in isArr" :key="index">{{item.text}}
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
              <td>分配值</td>
              <td>加速状态</td>
              <td>藏宝支付时间</td>
              <td>类型</td>
              <td>数据源ID</td>
              <td>生成宝藏图比例</td>
              <td>生成宝藏图时间</td>
              <td>分配任务卡张数</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.username}}</td>
              <td>{{item.userAddress}}</td>
              <td>{{item.total}}</td>
              <td>{{expediteStatusFn(item.expediteStatus)}}</td>
              <td v-html="timeStrDate(item.initCreateAt)"></td>
              <td>{{assignTypeFn(item.assignType)}}</td>
              <td>{{item.sourceId}}</td>
              <td>{{item.assignRate}}</td>
              <td v-html="timeStrDate(item.assignCreateAt)"></td>
              <td>{{item.mapCount}}</td>
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
        startTime: '',
        endTime: '',
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
      expediteStatusFn(index){
        return this.statusArr[index].text
      },
      assignTypeFn(index){
        return this.isArr[index].text
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=map.MapAssignRecord$ExpediteStatusEnum;map.MapAssignRecord$BountyTypeEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.ExpediteStatusEnum
            // this.isArr = res.data.data.BountyTypeEnum
          }
        })
      },
      resetFields() {
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
      commandChange (commend) {
        if (commend.index === 1) {
          this.equipment = commend.item.text
          this.clientType = commend.item.id
        } else if (commend.index === 2) {
          this.isText = commend.item.text
          this.deviceFlag = commend.item.id
        }
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/map/assign/queryMapAssignList', {
          username: this.userName, //
          id: this.id, //
          sourceId: this.sourceId,
          expediteStatus: this.clientType === 'selected' ? this.clientType = '' : this.clientType, //加速状态
          assignType: this.deviceFlag === 'selected' ? this.deviceFlag = '' : this.deviceFlag, //类型
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
