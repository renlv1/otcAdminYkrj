<template>
    <div class="c-page mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>用户确认定位</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">挖宝人</div>
                        <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="handleCommand">
                                <span class="el-dropdown-link">
                                  {{statusText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="item" v-for="(item,index) in statusEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box btns">
                        <div @click="resetFields()" class="btn black">清空</div>
                        <div class="btn" @click="search">查询</div>
                    </div>
                </div>
                <list-wrap :list="isListNull(list)" :total="total" @change="getData">
                    <div class="web-table">
                        <div class="center">
                            <div class="thead item-list">
                                <div class="li">ID</div>
                                <div class="li">挖宝人名</div>
                                <div class="li">挖宝人地址</div>
                                <div class="li">经度</div>
                                <div class="li">维度</div>
                                <div class="li">坐标位置</div>
                                <div class="li">状态</div>
                                <div class="li">创建时间</div>
                                <div class="li">IP</div>
                                <div class="li">所属地区</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.userName}}</div>
                                    <div class="li"><span>{{item.userAddress}}</span></div>
                                    <div class="li">{{item.longitude}}</div>
                                    <div class="li"><span>{{item.latitude}}</span></div>
                                    <div class="li"><span>{{item.address}}</span></div>
                                    <div class="li">{{statusFn(item.status)}}</div>
                                    <div class="li" v-html="timeStrDate(item.createAt)"><span></span></div>
                                    <div class="li">{{item.ip}}</div>
                                    <div class="li"><span v-if="item.whereMine === 1">国内</span><span v-else-if="item.whereMine === 2">国外</span></div>
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
    </div>
</template>

<script>
  export default {
    data() {
      return {
        id: '',
        userName: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        unfoldActive: 0,
        itemShow: false,
        statusEnum: [],
        statusText: '',
        statusId: '',
      }
    },
    created () {
      this.getType()
    },
    methods: {
      resetFields () {
        this.id = ''
        this.userName = ''
        this.statusText = ''
        this.statusId = ''
      },
      statusFn (length) {
        let arr = ['未确认位置','已确认位置','已分配任务','失效']
        return arr[length - 1]
      },
      getType () {
        let url = '/public/enumValue?classPackage=map.UserLocationRecord$StatusEnum;map.UserLocationRecord$WhereMineEnum&flag=true&state=4'
        this.$fetch.post(`${url}`).then(res => {
          if (res.msg === '成功') {
            this.statusEnum  = res.data.data.StatusEnum
          }
        })
      },
      handleCommand (commend) {
        this.statusText = commend.text
        this.statusId = commend.id
      },
      search () {
        this.total = 1
        this.getData()
      },
      getData (pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        let state = this.statusId === 'selected' ? '' : this.statusId
        if (this.total !== 0) {
          this.$fetch.post(`/map/location/queryLocationList`,{
            id: this.id,
            userName: this.userName,
            status : state,
            pageIndex: pageIndex || this.pageIndex,
            pageSize: this.pageSize
          }).then(res => {
            this.loadShow = false
            if (res.msg === '成功') {
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
    .wrap
        .crumb
            padding-bottom 22px;
            margin-bottom 0;
        .main-content
            width 100%;
            min-height calc(100vh - 147px)
            background-color #fff;
            padding 0 40px 0 24px;
            .web-table
                padding-top 0
                .item-list
                    width 100%
                    height 64px
                    line-height 64px
                    display flex
                    align-items center
                    justify-content flex-start
                    &:nth-child(n+2)
                        display block
                        height auto
                        line-height normal
                        width 100%
                        .flex-item
                            width 100%
                            height 64px
                            line-height 64px
                            display flex
                            align-items center
                            justify-content flex-start
                            transition all .3s linear
                            border-left 1px solid #DCDFE6
                            border-right 1px solid #DCDFE6
                            border-bottom 1px solid #DCDFE6
                            &:hover
                                background-color rgba(0,0,0, .1)
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        width auto
                        text-align center
                    &:first-child
                        background-color #edeff2;
                    .operation
                        color #2176ff
                        cursor pointer
                        display flex
                        align-items center
                        justify-content center
                        .in-btn
                            border-right 1px solid #DCDFE6
                            padding-right 10px
                            &:nth-child(n+2)
                                padding-left 10px
                                &:hover
                                    color #121212
                            &:last-child
                                border-right none
                            .icon
                                display inline-block
                                width 10px
                                height 6px
                                background url("~@img/unfold.png") no-repeat center
                                background-size cover
                                margin-left 6px
                                transition all .2s linear
                                &.active
                                    transform rotate(180deg)
                .table-list
                    height auto
    .search
        padding-top 20px
        position relative
        display flex
        flex-direction row
        flex-wrap wrap
        justify-content flex-start
        border-bottom 0
        .search-box
            width 318px
            height 34px
            line-height 34px
            margin: 4px 0 15px;
            display flex
            align-items center
            &:nth-child(7)
                .label
                    width 130px
                .input-wrap
                    flex 1
            .label
                padding-right 16px
                font-size 14px
            .input-wrap
                flex 1
                height 34px
                margin-right 40px
                input
                    border-radius 6px
                    width 100%
                    height 100%
                    margin-right 0
            .dropdown-wrap
                cursor pointer
                text-align center
                background-color #fff
                border: 1px solid #DCDFE6;
                padding: 0 10px;
                font-size 14px
                border-radius 6px
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
