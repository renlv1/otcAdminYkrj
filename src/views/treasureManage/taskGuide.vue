<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>任务指导</span>
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
          <div class="label">类型：</div>
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
          <div class="label">状态：</div>
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
          <div class="center">
            <div class="common-thead">
              <div class="thead" >ID</div>
              <div class="thead">用户名</div>
              <div class="thead tbody-address">用户地址</div>
              <div class="thead">图片集</div>
              <div class="thead">任务内容</div>
              <div class="thead">类型</div>
              <div class="thead">状态</div>
              <div class="thead">创建时间</div>
              <div class="thead">更新时间</div>
            </div>
            <div v-for="(item, index) in list" :key="index" class="tbody-wrap">
              <div class="common-tbody">
                <div class="tbody" >{{item.id}}</div>
                <div class="tbody">{{item.userName}}</div>
                <div class="tbody tbody-address">{{item.userAddress}}</div>
                <div class="tbody word-two more-text" @click="showTextDialog(item.imgList,1)">{{item.imgList || '-'}}</div>
                <div class="tbody word-two more-text" @click="showTextDialog(item.content,2)">{{item.content}}</div>
                <div class="tbody">{{typeFn(item.type)}}</div>
                <div class="tbody">{{activeFn(item.active)}}</div>
                <div class="tbody" v-html="timeStrDate(item.createAt)"></div>
                <div class="tbody" v-html="timeStrDate(item.updateAt)"></div>
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
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        addressDialog: false,
        showUserAddress: '',
        id: '',
        userName: '',//登录账户名
        sourceId: '',
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
        isArr:[], //
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
        textTitle: '',
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      showTextDialog(data,index){
        if (data) {
          let title = ''
          if(index === 1 ){
            title = '图片集'
          } else if(index === 2) {
            title = '任务内容'
          }
          Dialog ({
            title: title,
            content: data,
            type: 'confirm'
          })
        }
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
      
      typeFn(index){
        return this.statusArr[index].text
      },
      activeFn(index){
        return this.isArr[index].text
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=map.TaskGuide$TypeEnum;map.TaskGuide$ActiveEnum&flag=true&state=4`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.TypeEnum
            this.isArr = res.data.data.ActiveEnum
          }
        })
      },
      resetFields() {
        this.deviceFlag = ''
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
      // 状态
      handleCommand2(command) {
        this.isText = command.text
        this.deviceFlag = command.id
      },
      //类型
      handleCommand(command) {
        this.equipment = command.text
        this.clientType = command.id
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
        this.$fetch.post('/map/taskGuide/queryTaskGuideList', {
          userName: this.userName, //
          id: this.id, //
          type: this.clientType === 'selected' ? this.clientType = '' : this.clientType, //类型
          active: this.deviceFlag === 'selected' ? this.deviceFlag = '' : this.deviceFlag, //状态
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if(res.data) {
            this.list = res.data.page.list
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
