<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb detail-page">
      <div class="title-left">
        <router-link to="/treasureManage/bountyTask" class="title-m">赏金任务单 </router-link>
        <span> / 任务详情</span>
      </div>
      <div class="btn-right" v-if="dataDetail.status==2 && dataDetail.active ==1 && dataDetail.confirmStatus==0">
        <span class="pay-money" @click="resetTask()">重置任务</span>
        <span class="pay-money" @click="waBaoBtn()">系统挖宝</span>
        <span class="pay-money" @click="appointedTask()">指定任务</span>
      </div>
      <div class="btn-right" v-else-if="dataDetail.status==3 && dataDetail.active ==1 && dataDetail.confirmStatus==1">
        <span class="pay-money" @click="systemBtn()">系统确认任务</span>
      </div>
    </div>
    <div class="page-wrap detail-cont">
      <ul class="ul-w">
        <li class="li-item search">
          <div class="search-box">
            <div class="label">ID</div>
            <div class="input-wrap">{{dataDetail.id}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">用户名</div>
            <div class="input-wrap">{{dataDetail.userName}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">用户地址</div>
            <div class="input-wrap">{{dataDetail.userAddress  || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务人用户名</div>
            <div class="input-wrap">{{dataDetail.taskUserName || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务人用户地址</div>
            <div class="input-wrap">{{dataDetail.taskUserAddress || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">赏金ID</div>
            <div class="input-wrap">{{dataDetail.oneInitId || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务记录ID</div>
            <div class="input-wrap">{{dataDetail.taskId}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务纬度</div>
            <div class="input-wrap">{{dataDetail.latitude || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务经度</div>
            <div class="input-wrap">{{dataDetail.longitude || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务地点</div>
            <div class="input-wrap">{{dataDetail.taskAddress || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务具体地点</div>
            <div class="input-wrap">{{dataDetail.taskRealAddress || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">挖宝时间</div>
            <div class="input-wrap">{{dataDetail.taskTime}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">挖宝日期</div>
            <div class="input-wrap">{{weekFn(dataDetail.taskDate)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务描述</div>
            <div class="input-wrap">{{dataDetail.taskContent || '-'}}</div>
          </div>
        </li>
        <li class="li-item search" v-show="dataDetail.taskImgList">
          <div class="search-box">
            <div class="label">任务图列表</div>
            <div class="input-wrap">
              <ul class="ul-img">
                <li v-for="(imgUrl, index) in splitImg(dataDetail.taskImageList)" :key="index">
                  <img :src="imgUrl">
                </li>
              </ul>
              {{dataDetail.taskImageList}}
            </div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">状态</div>
            <div class="input-wrap">{{statusFn(dataDetail.status)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">激活状态</div>
            <div class="input-wrap">{{activeFn(dataDetail.active)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">确认状态</div>
            <div class="input-wrap">{{confirmFn(dataDetail.confirmStatus)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">加速标识</div>
            <div class="input-wrap">{{expediteFn(dataDetail.expediteFlag)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">赏金总金额</div>
            <div class="input-wrap">{{dataDetail.total}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">每张任务卡金额</div>
            <div class="input-wrap">{{dataDetail.everytotal}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">是否第一次打钱</div>
            <div class="input-wrap">{{dataDetail.isFirstGiveMoney === 'yes' ? '是' : '否'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">第一次挖出金额</div>
            <div class="input-wrap">{{dataDetail.firstOutTotal}}</div>
          </div>
        </li> 
        <li class="li-item search">
          <div class="search-box">
            <div class="label">第一次挖出百分比</div>
            <div class="input-wrap">{{dataDetail.firstOutPercent || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">第二次挖出金额</div>
            <div class="input-wrap">{{dataDetail.secondOutTotal || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">第二次挖出百分比</div>
            <div class="input-wrap">{{dataDetail.secondOutPercent || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">激活账单支付ID</div>
            <div class="input-wrap">{{dataDetail.activePayId || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">换任务次数</div>
            <div class="input-wrap">{{dataDetail.changeTaskCount || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">换任务账单支付ID</div>
            <div class="input-wrap">{{dataDetail.changeTaskPayId}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务卡生成时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.createTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务卡修改时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.updateTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">解锁时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.unlockTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">用户激活时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.activateTime) || '-'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">确认时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.confirmTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务确认时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.taskConfirmTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">分配任务时间</div>
            <div class="input-wrap">{{$changeDate(dataDetail.giveTaskTime)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">图标识</div>
            <div class="input-wrap">{{mapFn(dataDetail.mapFlag)}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">换任务经度</div>
            <div class="input-wrap">{{dataDetail.changeCurrentLon}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">换任务纬度</div>
            <div class="input-wrap">{{dataDetail.changeCurrentLat}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务来源</div>
            <div class="input-wrap">{{dataDetail.taskSource === 1 ? '系统' : '玩家'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">任务地方</div>
            <div class="input-wrap">{{dataDetail.taskWhereMine === 1 ? '国内' : '国外'}}</div>
          </div>
        </li>
        <li class="li-item search">
          <div class="search-box">
            <div class="label">备注</div>
            <div class="input-wrap">{{dataDetail.remark || '-'}}</div>
          </div>
        </li>
      </ul>
    </div>
    <el-dialog width="30%" :visible.sync="resetDialog">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>确认重置任务？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="resetDialog = false">取 消</el-button>
        <el-button type="primary" @click="resetSure()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="systemDialog">
      <div class="problem-box">
        <img src="../../../assets/img/warning.png"><label>系统确认任务？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="systemDialog = false">取 消</el-button>
        <el-button type="primary" @click="systemSure()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="waBaoDialog" title="系统干预">
      <ul class="ul-waBao">
        <li v-for="(item, index) in waBaoArr" :key="index" :class="{'active': chooseIndex === index}" @click="chooseBtn(index)">
          <div class="one-title">
            <div class="circle"></div>
            <div class="li-title">{{item.text}}</div>
          </div>
          <div class="li-cont">{{item.content}}</div>
        </li>
      </ul>
      <span slot="footer" class="dialog-footer">
        <el-button @click="waBaoDialog = false">取 消</el-button>
        <el-button type="primary" @click="waoBaoSure()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="appointDialog" :before-close="handleClose" title="指定任务">
      <div class="search">
        <div class="search-box">
          <div class="label">更换的任务ID</div>
          <div class="input-wrap"><input v-model="changeInitId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">更换的任务来源</div>
          <div class="input-wrap"><input v-model="source" type="text"></div>
        </div>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="handleClose()">取 消</el-button>
        <el-button type="primary" @click="appointSure()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  import { Message } from 'element-ui'
  export default {
    name: "transferLogDetail",
    data() {
      return{
        waBaoArr: [
          {text:'系统挖宝（系统确认）',content: '不需埋宝人确认，系统自动确认'},
          {text:'系统挖宝（用户确认）',content: '仍需埋宝人确认'}
        ],
        changeInitId: '',
        source: '',
        secondFlag:1,
        chooseIndex: 0,
        resetDialog: false,
        systemDialog: false,
        waBaoDialog: false,
        appointDialog: false,
        dataDetail: {},
        synStatusText: '',
        openfireText: '',
        openfire: '',//是否注册openfire用户
        genderText:  '',
        genderArr: [],//性别
        gender: '',
        statusArr: [],//状态
        activeArr: [],// 激活状态
        confirmArr: [],//确认状态
        expediteArr: [],//加速标识
        mapArr: [],//图标识
        statusText: '',
        tradeArr: [],//支付密码
        tradeText: '',
        mobileArr: [],//手机验证码
        mobileText: '',
        googleArr: [],//Gogole验证码
        googleText: '',
        userTypeArr: [],//用户类型
        userTypeText: '',
        loginPassword: '',
        contactInformationDialog: false,//修改用户上级弹窗
        showDialog: false,// 注销用户弹窗
        id: this.$route.query.id,
        refUserName: this.$route.query.refUserName,//上级用户名
        status: '',
        openTradePassword: '',//开启支付密码
        openMobileCode: '',//开启手机验证码
        openGoogleCode: '',//开启Google验证
        userType: '',//用户类型
        synStatus: '',//注册数据同步状态
      }
    },
    created() {
      this.getType()
      this.getData()
    },
    watch: {
      // Keyword: {
      //   handler(newName, oldName) {
      //     if (newName !== oldName) {
      //       this.bountyProductList = []
      //       this.isChange = true
      //     }
      //   },
      //   immediate: true
      // }
    },
    methods: {
      //指定任务
      appointSure(){
        this.$fetch.post('/map/map/changeMapTaskBySys',{
          oneMapId: this.id,
          changeInitId: this.changeInitId,
          source: this.source,
        }).then(res => {
          if(res.code === 0) {
            this.appointDialog  = false
            Dialog({
              title: '',
              content: res.msg
            })
          }
        })
      },
      appointedTask(){
        this.appointDialog = true
        this.changeInitId = ''
        this.source = ''
      },
      handleClose() {
        this.appointDialog = false
      },
      chooseBtn(index){
        this.chooseIndex = index
        this.secondFlag = index + 1
      },
      //系统挖宝
      waoBaoSure() {
        this.$fetch.post('/map/map/mineMapChangeTask',{
          oneMapId: this.id,
          secondFlag: this.secondFlag
        }).then(res => {
          if(res.code === 0) {
            this.chooseIndex = 0
            this.secondFlag = 1
            this.waBaoDialog  = false
            Dialog({
              title: '',
              content: res.msg
            })
          }
        })
      },
      waBaoBtn() {
        this.waBaoDialog = true
      },
      // 系统确认任务
      systemSure() {
        this.$fetch.post('/map/map/mineMapChangeTask',{
          oneMapId: this.id,
          secondFlag: 3
        }).then(res => {
          if(res.code === 0) {
            this.systemDialog  = false
            Dialog({
              title: '',
              content: res.msg
            })
          }
        })
      },
      systemBtn() {
        this.systemDialog = true
      },
      //重新分任务(清空任务)
      resetSure() {
        this.$fetch.post('/map/map/mineMapChangeTask',{
          oneMapId: this.id
        }).then(res => {
          if(res.code === 0) {
            this.resetDialog  = false
            Dialog({
              title: '',
              content: res.msg
            })
          }
        })
      },
      resetTask() {
        this.resetDialog  = true
      },
      splitImg(img) {
        let imgArr = []
        if (img) {
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
          let arr = data.split(',')
          let weekArr = []
          arr.forEach(item => {
            if (item === '1') weekArr.push('周一')
            if (item === '2') weekArr.push('周二')
            if (item === '3') weekArr.push('周三')
            if(item === '4') weekArr.push('周四')
            if(item === '5') weekArr.push('周五')
            if(item === '6') weekArr.push('周六')
            if(item === '7') weekArr.push('周日')
          })
          return weekArr.join('、')
        }
      },
      // 枚举
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=map.OneMapRecord$ActiveEnum;map.OneMapRecord$StatusEnum;map.OneMapRecord$ConfirmStatusEnum;map.OneMapRecord$MapFlagEnum;map.OneMapRecord$ExpediteFlagEnum;map.OneMapRecord$TaskSourceEnum;map.OneMapRecord$TaskWhereMineEnum;&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.StatusEnum
            this.activeArr = res.data.data.ActiveEnum
            this.confirmArr = res.data.data.ConfirmStatusEnum
            this.expediteArr = res.data.data.ExpediteFlagEnum
            this.mapArr = res.data.data.MapFlagEnum
          }
        })
      },
      verify() {
        // if(!this.sendUserName) {
        //   Message.error('请填写发送者用户名')
        //   return
        // }
        // if(!this.acceptUserName) {
        //   Message.error('请填写接收者用户名')
        //   return
        // }
        // if(!this.amount) {
        //   Message.error('请填写金额')
        //   return
        // }
        // if(!this.currencyText) {
        //   Message.error('请选择币种')
        //   return
        // }
        // if(!this.code) {
        //   Message.error('请填写安全码')
        //   return
        // }
        return true
      },
      getData(){
        this.$fetch.post(`/map/map/preCatMapData`,{
          id: this.id
        }).then(res => {
          this.dataDetail = res.data.resultData
        })
      },
      //状态
      statusFn(index){
        if (index === 1) return '未解锁'
        if (index === 2) return '已解锁'
        if (index === 3) return '待确认'
        if (index === 4) return '已完成'
        
        // return this.statusArr[index].text
      },
      //激活状态
      activeFn(index){
        if (index === 1) return '可用'
        if (index === 2) return '不可用'
        // return this.activeArr[index].text
      },
      //确认状态
      confirmFn(index){
        if (index === 0) return '未确认'
        if (index === 1) return '挖宝人完成确认'
        if (index === 2) return '埋宝人完成确认'
        // return this.confirmArr[index + 1].text
      },
      //加速标识
      expediteFn(index){
        if (index === 1) return '未加速完'
        if (index === 2) return '加速完'
        // return this.expediteArr[index].text
      },
      //图标识
      mapFn(index){
        if (index === 1) return '正常单'
        // return this.mapArr[index].text
      },
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  .ul-waBao
    margin-top 30px
    li
      margin-bottom 20px
      cursor pointer
      &.active
        .one-title
          .circle
            position relative
            border 1px solid #2176ff
            &:before
              border-radius 50%
              display block
              width 8px
              height 8px
              background #2176ff
              position absolute
              top 50%
              left 50%
              transform translate(-50%, -50%)
              content ''
      .li-cont
        margin-left 25px
        font-size 12px
        color #b2b3bc
      .one-title
        display flex
        .li-title
          font-size 14px
          color #121212
          margin-bottom 3px
        .circle
          margin-right 10px
          width 16px
          height 16px
          border-radius 50%
          border 1px solid #e5e8ed
  .ul-img{
    display flex
    li{
      width 40px
      height 40px
      margin-right 10px
      img{
        max-width 100%
        max-height 100%
      }
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
          width 70%!important
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
          width 70%
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
  .zoom-enter-to{
    transition: none;
  }
  .el-dropdown-menu{
    width 260px
  }
  .dialog-input{
    width 240px!important
  }
  .problem-box{
    display flex
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
          width 80%!important
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
  .wrap
    background-color: @white;
  .search
    padding-bottom 0
    position relative
    display flex
    flex-direction row
    flex-wrap wrap
    .search-box
      margin-bottom 0
      display flex
      flex-direction row
      align-items center
      .input-wrap
        width 280px
        input
          width 100%
        textarea
          width 260px
          height 100px
          padding 10px
          border 1px solid #DCDFE6
          background #fff
      .label
        width 200px
        color #b2b3bc
      .dropdown-wrap
        width 280px!important
        cursor pointer
        text-align center
        line-height 40px
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
  >>>.dropdown-wrap{
    min-width: 160px !important;
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
</style>>
