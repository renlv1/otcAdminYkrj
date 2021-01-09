<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>PK推送信息</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">套利id</div>
                        <div class="input-wrap"><input v-model="arbitrageid" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">信息类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{type || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in typeEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{status || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in statusEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box btns">
                        <div @click="resetClear()" class="btn black">清空</div>
                        <div class="btn" @click="search">查询</div>
                    </div>
                </div>
                <list-wrap :list="isListNull(list)" :total="total" @change="getData" :pageSize="pageSize">
                    <div class="web-table">
                        <table class="center">
                            <tr class="thead">
                                <td width="6%">ID</td>
                                <td>套利id</td>
                                <td>信息类型</td>
                                <td>状态</td>
                                <td>备注</td>
                                <td>创建时间</td>
                                <td>更新时间</td>
                            </tr>
                            <tr v-for="(item, index) in list" :key="index">
                                <td>{{item.id}}</td>
                                <td>{{item.arbitrageid}}</td>
                                <td>{{matchType(typeEnum,item.type)}}</td>
                                <td>{{matchType(statusEnum,item.status)}}</td>
                                <td>{{item.remark}}</td>
                                <td>{{item.createtime}}</td>
                                <td>{{item.updatetime}}</td>
                            </tr>
                            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
                                <img src="~@img/loading.gif" />
                            </div>
                        </table>
                    </div>
                </list-wrap>
            </div>
        </div>
    </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        id: '',
        arbitrageid: '',
        type: '',
        giveType: '',
        giveStatus: '',
        status: '全部',
        statusEnum: [],
        typeEnum: [],
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        total: 0,
        list: []
      }
    },
    created () {
      this.getType()
    },
    methods: {
      resetClear () {
        this.id = ''
        this.arbitrageid = ''
        this.type = '全部'
        this.status = '全部'
        this.giveType = ''
        this.giveStatus = ''
      },
      getType () {
        this.$fetch.get(`/public/enumValue?classPackage=message.AppSendMessage$TypeEnum;message.AppSendMessage$StatusEnum&flag=true&state=1`).then((res) => {
          if (res.msg === '成功') {
            this.statusEnum = res.data.data.StatusEnum // 状态
            this.typeEnum = res.data.data.TypeEnum // 信息类型
          }
        })
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.type = commend.item.text
          this.giveType = commend.item.id
        } else if (commend.index === 2) {
          this.status = commend.item.text
          this.giveStatus = commend.item.id
        }
      },
      // 查询
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        if (parseInt(this.total) !== 0) {
          this.loadShow = true
          this.$fetch.post(`/system/appSendInfo/queryAppSendMessageList`,{
            pageIndex: pageIndex || this.pageIndex,
            pageSize: pageSize || this.pageSize,
            id: this.id,
            arbitrageid: this.arbitrageid,
            type: this.giveType === 'selected' ? '' : this.giveType,
            status: this.giveStatus === 'selected' ? '' : this.giveStatus,
          }).then((res) => {
            if (res.msg === '成功') {
              this.loadShow = false
              this.list = res.data.page.list
              this.total = res.data.page.totalCount
            }
          })
        }
      }
    },

  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  @import "../../assets/css/var.less"
    .main-container
        padding 34px 20px 0 40px;
    .wrap
        background-color: @white;
        .crumb
            padding-bottom 22px;
            margin-bottom 0
        .main-content
            width 100%;
            background-color #fff;
            padding 0px 40px 0px 24px;
            min-height calc(100vh - 147px);
            .web-table
                td + td::before
                    display none
                & tr
                    &:first-child
                        background-color #edeff2 !important;
                        color @text !important;
                td
                  &:nth-child(6)
                      @media screen and (max-width 1200px)
                          flex none
                          width 80px
                    &:nth-child(7)
                        @media screen and (max-width 1200px)
                            flex none
                            width 80px
    .search
        position relative
        display flex
        flex-direction row
        flex-wrap wrap
        justify-content flex-start
        .search-box
            width 318px
            height 34px
            line-height 34px
            margin: 4px 0 15px;
            display flex
            align-items center
            &:nth-child(5)
                .input-wrap
                    margin-right 0
            &:nth-child(7)
                .input-wrap
                    flex none
            &:last-child
                width auto
            .label
                padding-right 16px
                font-size 14px
            .input-wrap
                flex 1
                height 34px
                margin-right 40px
                input
                    width 100%
                    height 100%
                    margin-right 0
                    border-radius 0
            .dropdown-wrap
                cursor pointer
                text-align center
                background-color #fff
                border: 1px solid #DCDFE6;
                border-radius: 0;
                padding: 0 10px;
                font-size 14px
                min-width: 130px !important;
                width: 200px !important;
                height: 34px !important;
                line-height: 34px !important;
                .el-dropdown
                    width 100%
                    height 100%
                    span
                        width 100%
                        display flex
                        align-items center
                        justify-content space-between
        .btns
            justify-content flex-start
            .btn
                cursor pointer
</style>
