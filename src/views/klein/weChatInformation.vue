<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>微信信息查询</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户名</div>
                        <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户地址</div>
                        <div class="input-wrap"><input v-model.trim="userAddress" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">在线状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="handleCommand">
                                <span class="el-dropdown-link">
                                  {{onLineText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="item" v-for="(item,index) in onLineEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box btns">
                        <div @click="resetFields()" class="btn black">清空</div>
                        <div class="btn" @click="search">查询</div>
                    </div>
                </div>
                <list-wrap :list="isListNull(list)" :total="total" @change="getData" :pageSize="pageSize">
                    <div class="web-table">
                        <div class="center">
                            <div class="thead item-list">
                                 <div class="li">ID</div>
                                 <div class="li">用户名</div>
                                 <div class="li">用户地址</div>
                                 <div class="li">微信唯一标识</div>
                                 <div class="li">微信用户名</div>
                                 <div class="li">微信昵称</div>
                                 <div class="li">微信头像地址</div>
                                 <div class="li">性别</div>
                                 <div class="li">选择模式</div>
                                 <div class="li">创建时间</div>
                                 <div class="li">修改时间</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                     <div class="li">{{item.id}}</div>
                                     <div class="li">{{item.userName}}</div>
                                     <div class="li"><span>{{item.userAddress}}</span></div>
                                     <div class="li">{{item.uin}}</div>
                                     <div class="li"><span>{{item.wxUserName}}</span></div>
                                     <div class="li">{{item.wxNickName}}</div>
                                     <div class="li"><span>{{item.wxImgUrl}}</span></div>
                                     <div class="li">{{sexFn(item.sex)}}</div>
                                     <div class="li">{{onLineStateFn(item.onLineState)}}</div>
                                     <div class="li"><span>{{item.createAt}}</span></div>
                                     <div class="li"><span>{{item.updateAt}}</span></div>
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
        userAddress: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        unfoldActive: 0,
        itemShow: false,
        onLineEnum: [],
        onLineText: '',
        onLineId: '',
      }
    },
    created () {
      this.getType()
    },
    methods: {
      resetFields () {
        this.id = ''
        this.userName = ''
        this.userAddress = ''
        this.onLineText = ''
      },
      sexFn (length) {
        if (String(length) === '-') {
          return '-'
        } else {
          let arr = ['男','女']
          return arr[length - 1]
        }
      },
      onLineStateFn (length) {
        if (String(length) === '-') {
          return '-'
        } else {
          let arr = ['线下','上线']
          return arr[length]
        }
      },
      getType () {
        this.$fetch.post(`/public/enumValue?classPackage=klein.WebChatInfo$OnLineStateEnum&flag=true&state=1`).then(res => {
          if (res.msg === '成功') {
            let data = res.data.data
            this.onLineEnum = data.OnLineStateEnum
          }
        })
      },
      handleCommand (commend) {
        this.onLineText = commend.text
        this.onLineId = commend.id
      },
      unfold (index) {
        this.unfoldActive = index
        this.itemShow = !this.itemShow
        if (this.unfoldActive !== index && this.itemShow === false) {
          this.itemShow = true
        }
        if (this.unfoldActive === index && this.itemShow === false) {
          this.itemShow = true
        }
        if (this.unfoldActive === index +'2' && this.itemShow === true) {
          this.itemShow = false
        }
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        this.loadShow = true
        let onLineState = parseInt(this.onLineId, 10) >= 0 ?  parseInt(this.onLineId, 10) : ''
        if (this.total !== 0) {
          this.$fetch.post(`/klein/webChatInfo/queryWebChatAuth`,{
            id: this.id,
            userName: this.userName,
            userAddress: this.userAddress,
            onLineState: onLineState,
            pageIndex: pageIndex || this.pageIndex,
            pageSize: pageSize || this.pageSize,
          }).then(res => {
            this.loadShow = false
            if (res.msg === '成功') {
              this.list = res.data.page.list
              this.total = res.data.page.totalCount
            }
          })
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    .wrap
        .crumb
            padding-bottom 22px;
            margin-bottom 0;
        .main-content
            width 100%;
            background-color #fff;
            padding 0 40px 0 24px;
            min-height calc(100vh - 147px);
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
                        .on-the-details
                            min-height 0
                            opacity 0
                            overflow hidden
                            .info-box
                                width 100%
                                height 100%
                                .item-flex-item
                                    display flex
                                    align-items center
                                    justify-content flex-start
                                    text-align center
                                    height 40px
                                    line-height 40px
                                    padding 0 22px
                                    .li
                                        flex none
                                        width auto
                                        margin-right 100px
                                        text-align left
                                        &:nth-child(1)
                                            padding-left 0
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        text-align center
                    .li
                        &:nth-child(5)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:4;
                        &:nth-child(7)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:4;
                        &:nth-child(10)
                             padding-right 5px
                        &:nth-child(11)
                            padding-left: 5px
                            padding-right 20px
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
                    .item-table
                        width 100%
                        position relative
                        .on-the-details
                            position absolute
                            width 100%
                            height 80px
                            left 0
                            right 0
                            bottom -80px
                .table-list
                    height auto
    .search
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
