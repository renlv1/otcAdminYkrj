<template>
  <div class="c-page mar-right transaction-list">
    <div class="crumb">
      <span>查询安全问题</span>
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
          <div class="label">用户地址：</div>
          <div class="input-wrap"><input v-model.trim="userAddress" type="text"></div>
        </div>
        <div class="search-box">
          <div class="label">问题类型：</div>
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
                {{ isText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
              </span>
              <el-dropdown-menu class="menu" slot="dropdown">
                <el-dropdown-item :command="composeValue(item, 2)" v-for="(item, index) in isArr" :key="index">{{item.text}}</el-dropdown-item>
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
      <list-wrap :list="list" :total="total" @change="getData">
        <div class="web-table">
          <div class="center">
            <div class="common-thead">
              <div class="thead">ID</div>
              <div class="thead">用户名</div>
              <div class="thead">用户地址</div>
              <div class="thead">问题类型</div>
              <div class="thead">状态</div>
              <div class="thead">问题</div>
              <div class="thead">答案</div>
              <div class="thead">备注</div>
              <div class="thead">创建时间</div>
              <div class="thead">更新时间</div>
            </div>
            <div v-for="(item, index) in list" :key="index" class="tbody-wrap">
              <div class=" common-tbody">
                <div class="tbody">{{item.id}}</div>
                <div class="tbody">{{item.username}}</div>
                <div class="tbody">{{item.address}}</div>
                <div class="tbody">{{$type(item.type)}}</div>
                <div class="tbody">{{$status(item.status)}}</div>
                <div class="tbody">{{item.question}}</div>
                <div class="tbody">{{item.answer}}</div>
                <div class="tbody">{{item.remark || '-'}}</div>
                <div class="tbody" v-html="timeStrDate(item.createtime)"></div>
<!--                <div  @click="upload(item)" class="blue-td cursor tbody" v-show="item.packFlag === false"> <span>展开</span><img src="../../../src/assets/img/kai.png"></div>-->
<!--                <div  @click="shouqi(item)" class="blue-td cursor tbody" v-show="item.packFlag === true"> <span>收起</span><img src="../../../src/assets/img/shou.png"></div>-->
                <div  class="tbody" v-html="timeStrDate(item.updatetime)"></div>
              </div>
              <!--              v-show="item.packFlag === true"-->
<!--              <div class="add-time" v-show="item.packFlag === true"> 更新时间 ：{{item.updatetime}}</div>-->
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
  // import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        id: '',
        userName: '',//用户名
        userAddress: '',//用户地址
        statusArr: [],
        equipment: '',
        isArr:[],
        isText: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        isAuthor: '',//问题类型
        isRead: ''//状态
      }
    },
    created() {
      this.getType()
      // this.getData()
    },
    methods: {
      upload(item) {
        item.packFlag = !item.packFlag
      },
      shouqi(item) {
        item.packFlag = !item.packFlag
      },
      getType() {
        this.$fetch.get(`/public/enumValue?classPackage=user.SafeProblem$TypeEnum;user.SafeProblem$StatusEnum;&flag=true&state=1`).then(res => {
          if (res.code === 0) {
            this.statusArr = res.data.data.TypeEnum
            this.isArr = res.data.data.StatusEnum
          }
        })
      },
      resetFields() {
        this.userAddress = ''
        this.id = ''
        this.userName = ''
        this.isAuthor = ''
        this.isText = ''
        this.equipment = ''
        this.isRead = ''
      },
      commandChange (commend) {
        let command = commend.item
        let id = commend.index
        if (id === 1) {
          this.equipment = command.text
          this.isAuthor = command.id
        } else if (id === 2) {
          this.isText = command.text
          this.isRead = command.id
        }
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/user/safeProblem/querySafeProblemList', {
          id: this.id,
          username: this.userName, //用户名
          address: this.userAddress, //用户地址
          type: this.isAuthor === 'selected' ? this.isAuthor = '' : this.isAuthor, //问题类型
          status: this.isRead === 'selected' ? this.isRead = '' : this.isRead,//状态
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize
        }).then((res) => {
          this.loadShow = false
          if(res.data) {
            let list = res.data.page.list
            list.forEach(item => {
              item.packFlag = false
            })
            this.list = list
            this.total = res.data.page.totalCount
          } else {
            this.list = []
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
  .time
    text-align left
  .tbody-tr
    display: flex
    align-items center
    div
      flex 1
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
