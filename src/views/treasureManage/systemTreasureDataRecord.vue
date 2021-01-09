<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>系统藏宝数据记录</span>
    </div>
    <div class="page-wrap">
      <div class="search">
        <div class="search-box">
          <div class="label">ID：</div>
          <div class="input-wrap"><input v-model="userId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">用户名：</div>
          <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">地区：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="handleCommand2">
              <span class="el-dropdown-link">
                {{totaltext || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="item" v-for="(item, index) in typeArr" :key="index">{{item.text}}
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
          <div class="center">
            <div class="common-thead">
              <div class="thead">ID</div>
              <div class="thead">用户名</div>
              <div class="thead">经度</div>
              <div class="thead">纬度</div>
              <div class="thead tbody-address">埋宝地点</div>
              <div class="thead tbody-address">具体地点名</div>
              <div class="thead">地区</div>
              <div class="thead">挖宝时间</div>
              <div class="thead">生成时间</div>
              <div class="thead">操作</div>
            </div>
            <div v-for="(item, index) in isListNull(list)" :key="index" class="tbody-wrap">
              <div class=" common-tbody">
                <div class="tbody">{{item.id}}</div>
                <div class="tbody">{{item.userName}}</div>
                <div class="tbody">{{item.taskLongitude}}</div>
                <div class="tbody">{{item.taskLatitude}}</div>
                <div class="tbody tbody-address word-two more-text" @click="showTextDialog(item.taskAddress,1)">{{item.taskAddress}}</div>
                <div class="tbody tbody-address word-two more-text" @click="showTextDialog(item.taskRealAddress,2)">{{item.taskRealAddress || '-'}}</div>
                <div class="tbody">{{whereMineFn(item.whereMine)}}</div>
                <div class="tbody">{{item.taskTime}}</div>
                <div class="tbody" v-html="timeStrDate(item.createAt)"></div>
                <div @click="upload(item)" class="blue-td cursor tbody" v-show="item.packFlag === false"> <span>展开</span><img src="../../../src/assets/img/kai.png"></div>
                <div @click="shouqi(item)" class="blue-td cursor tbody" v-show="item.packFlag === true"> <span>收起</span><img src="../../../src/assets/img/shou.png"></div>
              </div>
              <div class="add-more" :class="{'add-box': item.taskImgList}" v-show="item.packFlag === true">
                <div class="add-up">
                  <span>任务描述: {{item.taskContent}}</span>
                  <span>更新时间: {{$changeDate(item.updateAt,'-',8)}}</span>
                  <span>挖宝日期: {{weekFn(item.taskDate)}}</span>
                  <span>坐标的geohash数据: {{item.geoHash}}</span>
                  <span class="img-td" v-show="item.taskImgList">
                    图片:
                    <ul>
                      <li v-for="(imgUrl, index) in splitImg(item.taskImgList)" :key="index">
                          <img v-if="imgUrl && imgUrl !== '-'" :src="imgUrl">
                          <i class="null" v-else>-</i>
                      </li>
                    </ul>
                  </span>
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
        addressDialog: false,
        showUserAddress: '',
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
        pickerOptionsStart2: {
          disabledDate: time => {
            if (this.overTime) {
              return time.getTime() > new Date(this.overTime).getTime()
            }
          }
        },
        pickerOptionsEnd2: {
          disabledDate: time => {
            if (this.beginTime) {
              return time.getTime() < new Date(this.beginTime).getTime() - 86400000
            }
          }
        },
        textTitle:'',
        typeArr: [],
        statusArr2: [],
        shareName: '',
        bossName2: '',
        bossName: '',
        userId: '',
        userName: '',
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
        orderArr: [],
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
      }
    },
    created() {
      this.getType()
    },
    methods: {
      showTextDialog(data,index){
        if (data) {
          let title = ''
          if(index === 1 ){
            title = '埋宝地点'
          } else if(index === 2) {
            title = '具体地点名'
          }
          Dialog ({
            title: title,
            content: data,
            type: 'confirm'
          })
        }
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=map.SystemInitRecord$WhereMineEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.typeArr = res.data.data.WhereMineEnum
          }
        })
      },
      splitImg(img) {
        let imgArr = []
        if(img) {
          if(img.indexOf(',') > -1) {
            imgArr = img.split(',')
          } else {
            imgArr.push(img)
          }
          return imgArr
        }
      },
      weekFn(data){
        if(data) {
          let newArr = data.split(',')
          let weekArr = []
          newArr.forEach(item => {
            if (item === '1') weekArr.push('周一')
            if (item === '2') weekArr.push('周二')
            if (item === '3') weekArr.push('周三')
            if(item === '4') weekArr.push('周四')
            if(item === '5') weekArr.push('周五')
            if(item === '6') weekArr.push('周六')
            if(item === '7') weekArr.push('周日')
          })
          return weekArr.join(',')
        }
      },
      whereMineFn(index) {
        return this.typeArr[index].text
      },
      handleCommand2(command) {
        this.totaltext = command.text
        this.statusType = command.id
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
      // 新增
      paySure() {
        if(this.verify()) {
          this.$fetch.post('/finance/upServerBoss/updateUpServerBossInfo',{
            userName : this.userName,
            status: this.currency,
            bossName : this.shareName ,
          }).then(res => {
            if(res.code === 0) {
              Dialog({
                title: '',
                content: res.msg,
                okFn: () => {
                  this.getData()
                }
              })
              this.handleClose()
            } else {
              this.handleClose()
            }
          }).catch(err => {
            this.handleClose()
            console.log(err)
          })
        }
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
      addBtn() {
        this.shareName = ''
        this.username = ''
        this.endTime = ''
        this.minAmount = ''
        this.beginTime = ''
        this.overTime = ''
        this.curText = ''
        this.showDialog = true
        this.currency = ''
      },
      resetFields() {
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
        this.$fetch.post('/map/sysInit/querySysInitList', {
          id: this.userId,
          userName: this.userName, //
          whereMine:this.statusType === 'selected' ? this.statusType = '' : this.statusType,
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
          } else {
            this.list = []
            this.total = 0
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
  .web-table
      .add-more
          .add-up
              span
                  line-height 30px
                  margin-bottom 0
</style>
