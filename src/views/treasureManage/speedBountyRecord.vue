<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>加速赏金单记录</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="userId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">被加速用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">下级用户名：</div>
          <div class="input-wrap"><input v-model.trim="childUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">被加速任务卡ID：</div>
          <div class="input-wrap"><input v-model="mapId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">加速来源用户名：</div>
          <div class="input-wrap"><input v-model.trim="realSourceUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">加速来源ID：</div>
          <div class="input-wrap"><input v-model="realSourceAssignId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">加速标识：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{typetext || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 1)" v-for="(item, index) in typeArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">加速类型：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{totaltext || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in orderArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">时间：</div>
          <el-date-picker
            placeholder="开始时间"
            v-model="startTime"
            type="datetime"
            :picker-options="pickerOptionsStart"
            default-time="00:00:00"
            format="yyyy-MM-dd HH:mm:ss"
          >
          </el-date-picker>
          
          <span style="margin-right: 10px">到</span>
          
          <el-date-picker
            placeholder="结束时间"
            v-model="endTime"
            type="datetime"
            format="yyyy-MM-dd HH:mm:ss"
            :picker-options="pickerOptionsEnd"
            default-time="23:59:59">
          >
          </el-date-picker>
        </div>
        <div class="f1"></div>
        <div class="btns">
          <div @click="resetFields()" class="btn black">清空</div>
          <div class="btn" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="isListNull(list)" :total="total" @change="getData">
        <div class="web-table">
          <div class="center">
            <div class="common-thead">
              <div class="thead">ID</div>
              <div class="thead">被加速用户名</div>
              <div class="thead">下级用户名</div>
              <div class="thead">被加速的任务ID</div>
              <div class="thead">赏金单原解锁时间</div>
              <div class="thead">被加速天数</div>
              <div class="thead">加速后解锁时间</div>
              <div class="thead">操作</div>
            </div>
            <div v-for="(item, index) in list" :key="index" class="tbody-wrap">
              <div class="common-tbody">
                <div class="tbody">{{item.id}}</div>
                <div class="tbody">{{item.userName}}</div>
                <div class="tbody">{{item.childUserName}}</div>
                <div class="tbody">{{item.mapId}}</div>
                <div class="tbody" v-html="timeStrDate(item.mineMapBeforeDate)" ></div>
                <div class="tbody">{{item.expediteDay}}</div>
                <div class="tbody" v-html="timeStrDate(item.mineMapAfterDate)" ></div>
                <div @click="upload(item)" class="blue-td cursor tbody" v-show="item.packFlag === false"> <span>展开</span><img src="../../../src/assets/img/kai.png"></div>
                <div @click="shouqi(item)" class="blue-td cursor tbody" v-show="item.packFlag === true"> <span>收起</span><img src="../../../src/assets/img/shou.png"></div>
              </div>
              <div class="add-more" v-show="item.packFlag === true">
                <div class="add-up">
                  <span>加速来源记录ID: {{item.realSourceAssignId}}</span><span>加速值: {{item.expediteAmount}}</span> <span>实际可参与加速值: {{item.realExpediteAmount}}</span> <span>统计标识: {{totalFlagFn(item.totalFlag)}}</span>
                  <span>加速标识: {{expediteFlagFn(item.expediteFlag)}}</span> <span>加速类型: {{expediteTypeFn(item.expediteType)}}</span> <span>加速来源用户名: {{item.realSourceUserName}}</span>
                  <span>创建时间: {{$changeDate(item.createAt)}}</span>  <span>实际加速消耗值: {{item.expediteRealUseAmount}}</span> <span>加速消耗值: {{item.expediteUseAmount}}</span>
                </div>
              </div>
            </div>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </div>
        </div>
      </list-wrap>
    </div>
  </div>
</template>

<script>
  import { Message } from 'element-ui'
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        pickerOptionsStart: {
          disabledDate: (time) => {
            let beginDateVal = this.endTime
            if (beginDateVal) {
              return time.getTime() > beginDateVal
            }
          }
        },
        pickerOptionsEnd: {
          disabledDate: (time) => {
            let beginDateVal = this.startTime
            if (beginDateVal) {
              return time.getTime() < beginDateVal
            }
          }
        },
        orderArr: [
          {id:'',text: '全部'},
          {id:1,text: '正常藏宝'}
        ],
        typetext: '',
        typeArr: [],
        statusArr2: [],
        shareName: '',
        bossName2: '',
        bossName: '',
        userId: '',
        userName: '',
        childUserName: '',
        mapId: '',
        realSourceUserName: '',
        realSourceAssignId: '',
        statusArr: [],
        statusText: '',
        transfertoBalance:'',//转到余额
        curren: '',//币种
        balanceEntrustFreeze: '',//委托冻结余额
        blockedBalanceAmout:'',//冻结余额
        balanceAmout: '',//余额
        accountUser: '',//账户用户名
        userDialog: false,
        beginTime: '',
        overTime: '',
        minAmount: '',
        curText: '',
        currencyText: '',
        currencyArr: [],
        id: '',
        startTime: '',
        endTime: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        showDialog: false,
        userAddress: '',
        upperName: '',
        refAddress: '',
        currency: '',
        idValue: '',
        statusType: '',
        totaltext: '',
        expediteFlag: '',
        TotalFlagArr: [],
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=map.ExpediteMapRecord$ExpediteFlagEnum;map.ExpediteMapRecord$TotalFlagEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.typeArr = res.data.data.ExpediteFlagEnum
            this.TotalFlagArr = res.data.data.TotalFlagEnum
          }
        })
      },
      expediteFlagFn(index){
        return this.typeArr[index].text
      },
      totalFlagFn(index) {
        return this.TotalFlagArr[index].text
      },
      expediteTypeFn(index){
        return this.orderArr[index].text
      },
      handleCommand2(command) {
        this.totaltext = command.text
        this.statusType = command.id
      },
      // 标识
      handleCommand(command) {
        this.typetext = command.text
        this.expediteFlag = command.id
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.typetext = commend.item.text
          this.expediteFlag = commend.item.id
        } else if (commend.index === 2) {
          this.totaltext = commend.item.text
          this.statusType = commend.item.id
        }
      },
      upload(item) {
        item.packFlag = !item.packFlag
      },
      shouqi(item) {
        item.packFlag = !item.packFlag
      },
      verify() {
        if(!this.username) {
          Message.error('请填写用户名')
          return
        }
        if(!this.currencyText || this.currencyText === 'selected') {
          Message.error('请选择状态')
          return
        }
        if(!this.shareName) {
          Message.error('请填写共享者名称')
          return
        }
        return true
      },
      handleClose2() {
        this.userDialog = false
      },
      handleClose() {
        this.currencyText = ''
        this.beginTime = ''
        this.overTime = ''
        this.curText = ''
        this.showDialog = false
        this.currency = ''
      },
      resetFields() {
        this.typetext = ''
        this.expediteFlag = ''
        this.realSourceAssignId = ''
        this.mapId = ''
        this.childUserName = ''
        this.realSourceUserName = ''
        this.totaltext = ''
        this.refAddress = ''
        this.upperName = ''
        this.statusText = ''
        this.statusType = ''
        this.bossName = ''
        this.userAddress = ''
        this.state = ''
        this.currencyText = ''
        this.currency = ''
        this.userId = ''
        this.userName = ''
        this.startTime = ''
        this.endTime = ''
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/map/expediteMap/queryExpediteMapList', {
          id: this.userId,
          userName: this.userName, //
          childUserName: this.childUserName,
          realSourceUserName: this.realSourceUserName,
          mapId: this.mapId,
          realSourceAssignId: this.realSourceAssignId,
          expediteFlag: this.expediteFlag === 'selected' ? this.expediteFlag = '' : this.expediteFlag, //加速标识
          expediteType: this.statusType === 'selected' ? this.statusType = '' : this.statusType,
          startCreateAtStr: this.$changeDate(this.startTime, '-', 8),
          endCreateAtStr: this.$changeDate(this.endTime, '-', 8),
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if( res.data) {
            this.loadShow = false
            let list = res.data.page.list
            list.forEach(item => {
              item.packFlag = false
            })
            this.list = list
            this.total = res.data.page.totalCount
          }
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
  .problem-box{
    display flex
    justify-content space-between
    align-items center
    .dropdown-wrap {
      cursor pointer
      text-align center
      line-height 40px
      width: 250px;
      margin-right: 20px;
      background-color #fff
      border: 1px solid #DCDFE6;
      border-radius: 6px;
      padding: 0 10px;
      font-size 15px
      height: 40px;
      >>>.el-dropdown-link{
        display flex
        justify-content space-between
        align-items center
      }
    }
    >>>.el-dropdown-menu{
      width 240px!important
    }
  }
  >>>.el-dialog__body{
    padding 0 20px!important
    .search{
      padding-bottom 0
      .search-box{
        width 100%
        margin-top 0
        margin-bottom 20px
        .el-input{
          display flex
          align-items center
          .el-input__inner{
            margin-right 0!important
            width 100%!important
          }
          .el-input__icon{
            line-height 35px!important
          }
        }
        .curreny-wrap{
          cursor pointer
          text-align center
          line-height 40px
          background-color #fff
          border: 1px solid #DCDFE6;
          border-radius: 6px;
          padding: 0 10px;
          font-size 15px
          height: 40px;
        }
        .input-wrap{
          width 80%
          &.input-main{
            width 70%
          }
          input{
            margin-right 0!important
            width 100%
          }
        }
      }
    }
  }
  .dialog-input{
    width 250px
  }
  .el-dropdown-menu{
    max-height 300px!important
    overflow auto !important
    /*max-width 200px!important*/
  }
  .payment{
    max-width 210px!important
  }
  .crumb
    display flex
    justify-content space-between
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
