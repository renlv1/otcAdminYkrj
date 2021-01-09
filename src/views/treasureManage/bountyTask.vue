<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>赏金任务单</span>
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
          <div class="label">任务人用户名：</div>
          <div class="input-wrap"><input v-model.trim="taskUserName" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">赏金ID：</div>
          <div class="input-wrap"><input v-model="sourceId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">任务记录ID：</div>
          <div class="input-wrap"><input v-model="taskId" type="number"></div>
        </div>
        <div class="search-box">
          <div class="label">状态：</div>
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
          <div class="label">激活状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{isText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in activeArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">确认状态：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{confirmText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 3)" v-for="(item, index) in confirmArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">加速标识：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{expediteText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 4)" v-for="(item, index) in expediteArr" :key="index">{{item.text}}
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
        <div class="search-box">
          <div class="label">图标识：</div>
          <div class="input-wrap dropdown-wrap">
            <el-dropdown @command="commandChange">
              <span class="el-dropdown-link">
                {{mapText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 5)" v-for="(item, index) in mapArr" :key="index">{{item.text}}
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
              <td>任务人用户名</td>
              <td width="6%">赏金ID</td>
              <td width="6%">任务记录ID</td>
              <td>任务经度</td>
              <td>任务纬度</td>
              <td width="15%">任务地点</td>
              <td>挖宝时间</td>
              <td width="15%">挖宝日期</td>
              <td>操作</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.id}}</td>
              <td>{{item.userName}}</td>
              <td>{{item.taskUserName}}</td>
              <td>{{item.oneInitId}}</td>
              <td>{{item.taskId}}</td>
              <td>{{item.longitude}}</td>
              <td>{{item.latitude}}</td>
              <td @click="showTextDialog(item.taskAddress)"><div class="word-two more-text">{{item.taskAddress}}</div></td>
              <td>{{item.taskTime}}</td>
              <td>{{$weekFn(item.taskDate) || '-'}}</td>
              <td class="blue-td" v-if="item.status==2 && item.active ==1 && item.confirmStatus==0">
                <router-link tag="a" :to="{path: '/treasureManage/bountyTask/bountyTaskDetail',query: {id: item.id}}">查看 <span class="grey">| </span> </router-link>
                <span @click="resetTask(item.id)">重置任务 <span class="grey">| </span></span>
                <span @click="waBaoBtn(item.id)">系统挖宝 <span class="grey">| </span></span>
                <span @click="appointedTask(item.id)">指定任务</span>
              </td>
              <td class="blue-td" v-else-if="item.status==3 && item.active ==1 && item.confirmStatus==1">
                <router-link tag="a" :to="{path: '/treasureManage/bountyTask/bountyTaskDetail',query: {id: item.id}}">查看 <span class="grey">| </span> </router-link>
                <span @click="systemBtn(item.id)">系统确认任务 </span>
              </td>
              <td class="blue-td" v-else>
                <router-link tag="a" :to="{path: '/treasureManage/bountyTask/bountyTaskDetail',query: {id: item.id}}">查看  </router-link>
              </td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
    </div>
    <el-dialog width="30%" :visible.sync="resetDialog">
      <div class="problem-box">
        <img src="../../assets/img/warning.png"><label>确认重置任务？</label>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="resetDialog = false">取 消</el-button>
        <el-button type="primary" @click="resetSure()">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog width="30%" :visible.sync="systemDialog">
      <div class="problem-box">
        <img src="../../assets/img/warning.png"><label>系统确认任务？</label>
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
  export default {
    data() {
      return {
        addressDialog:false,
        showUserAddress: '',
        changeInitId: '',
        source: '',
        waBaoArr: [
          {text:'系统挖宝（系统确认）',content: '不需埋宝人确认，系统自动确认'},
          {text:'系统挖宝（用户确认）',content: '仍需埋宝人确认'}
        ],
        appointDialog:false,
        waBaoDialog: false,
        systemDialog: false,
        resetDialog: false,
        id: '',
        taskUserName:'',
        userName: '',//登录账户名
        sourceId: '',
        taskId: '',
        accountAddress: '',//登录位置IP
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
        activeArr: [],
        confirmArr: [],
        confirmText: '',
        confirmStatus: '',
        expediteArr: [],
        expediteText: '',
        expediteFlag: '',
        mapArr: [],
        mapText: '',
        mapFlag: '',
        oneMapId: '',
        chooseIndex: 0,
        secondFlag: 1
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      showTextDialog(data){
        if (data) {
          Dialog ({
            title: '任务地点',
            content: data,
            type: 'confirm'
          })
        }
      },
      //指定任务
      appointSure(){
        this.$fetch.post('/map/map/changeMapTaskBySys',{
          oneMapId: this.oneMapId,
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
      appointedTask(id){
        this.oneMapId = id
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
          oneMapId: this.oneMapId,
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
      waBaoBtn(id) {
        this.oneMapId = id
        this.waBaoDialog = true
      },
      // 系统确认任务
      systemSure() {
        this.$fetch.post('/map/map/mineMapChangeTask',{
          oneMapId: this.oneMapId,
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
      systemBtn(id) {
        this.oneMapId = id
        this.systemDialog = true
      },
      //重新分任务(清空任务)
      resetSure() {
        this.$fetch.post('/map/map/mineMapChangeTask',{
          oneMapId: this.oneMapId
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
      resetTask(id) {
        this.oneMapId = id
        this.resetDialog  = true
      },
      statusFn(index){
        return this.statusArr[index + 1].text
      },
      activeFn(index){
        return this.activeArr[index].text
      },
      confirmFn(index){
        return this.confirmArr[index + 1].text
      },
      expediteFn(index){
        return this.expediteArr[index].text
      },
      mapFn(index){
        return this.mapArr[index].text
      },
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
      weekFn(data) {
        if(data){
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
      resetFields() {
        this.mapFlag = ''
        this.mapText = ''
        this.expediteFlag = ''
        this.expediteText = ''
        this.taskUserName = ''
        this.id = ''
        this.userName = ''
        this.sourceId = ''
        this.taskId = ''
        this.accountAddress = ''
        this.clientType = ''
        this.startTime = ''
        this.endTime = ''
        this.isText = ''
        this.equipment = ''
        this.deviceFlag = ''
        this.confirmText = ''
        this.confirmStatus = ''
      },
      commandChange (commend) {
        let num = commend.index
        if (num === 1) {
          this.equipment = commend.item.text
          this.clientType = commend.item.id
        } else if (num === 2) {
          this.isText = commend.item.text
          this.deviceFlag = commend.item.id
        } else if (num === 3) {
          this.confirmText = commend.item.text
          this.confirmStatus = commend.item.id
        } else if (num === 4) {
          this.expediteText = commend.item.text
          this.expediteFlag = commend.item.id
        } else if (num === 5) {
          this.mapText = commend.item.text
          this.mapFlag = commend.item.id
        }
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/map/map/queryOneMapList', {
          userName: this.userName, //
          id: this.id, //
          taskUserName: this.taskUserName,
          oneInitId: this.sourceId,
          taskId: this.taskId,
          status: this.clientType === 'selected' ? this.clientType = '' : this.clientType, //加速状态
          active: this.deviceFlag === 'selected' ? this.deviceFlag = '' : this.deviceFlag, //类型
          confirmStatus:  this.confirmStatus === 'selected' ? this.confirmStatus = '' : this.confirmStatus, //确认状态
          expediteFlag:  this.expediteFlag === 'selected' ? this.expediteFlag = '' : this.expediteFlag, //加速标识
          mapFlag:  this.mapFlag === 'selected' ? this.mapFlag = '' : this.mapFlag, //图标识
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if(res.code === 0) {
            if(res.data) {
              this.list = res.data.page.list
              this.total = res.data.page.totalCount
            } else {
              this.list = []
              this.total = 0
            }
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
