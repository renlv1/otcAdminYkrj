<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>微信授权重要信息</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户地址</div>
                        <div class="input-wrap"><input v-model.trim="userAddress" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">UUID</div>
                        <div class="input-wrap"><input v-model.trim="uuid" type="text"></div>
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
                                 <div class="li">用户钱包地址</div>
                                 <div class="li">微信uuid</div>
                                 <div class="li">uin</div>
                                 <div class="li">sid</div>
                                 <div class="li">skey</div>
                                 <div class="li">cookie</div>
                                 <div class="li">synckey</div>
                                 <div class="li">passTicket</div>
                                 <div class="li">当前微信域名</div>
                                 <div class="li">创建时间</div>
                                 <div class="li">修改时间</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                     <div class="li">{{item.id}}</div>
                                     <div class="li"><span>{{item.userAddress}}</span></div>
                                     <div class="li"><span>{{item.uuid}}</span></div>
                                     <div class="li">{{item.uin}}</div>
                                     <div class="li"><span>{{item.sid}}</span></div>
                                     <div class="li"><span>{{item.skey}}</span></div>
                                     <div class="li"><span>{{item.cookie}}</span></div>
                                     <div class="li">{{item.synckey}}</div>
                                     <div class="li"><span>{{item.passTicket}}</span></div>
                                     <div class="li">{{item.wxDomain}}</div>
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
        userAddress: '',
        uuid: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
      }
    },
    methods: {
      resetFields () {
        this.id = ''
        this.userAddress = ''
        this.uuid = ''
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        this.loadShow = true
         if (this.total !== 0 ) {
           this.$fetch.post(`/klein/webChatAuth/queryWebChatAuth`,{
             id: this.id,
             userAddress: this.userAddress,
             uuid: this.uuid,
             pageIndex: pageIndex || this.pageIndex,
             pageSize: pageSize || this.pageSize,
           }).then(res => {
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
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        width auto
                        text-align center
                    .li
                        &:nth-child(1)
                            @media screen and (max-width 1200px) {
                                flex none
                                width: 60px
                            }
                        /*&:nth-child(3)
                            span
                                display inline-block
                                width 60px*/
                        &:nth-child(5)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:2;
                        &:nth-child(6)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden; 
                                text-overflow:ellipsis;
                                display:-webkit-box; 
                                -webkit-box-orient:vertical; 
                                -webkit-line-clamp:2;
                        &:nth-child(7)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:2;
                        &:nth-child(9)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:2;
                        &:nth-child(11)
                            padding-right 5px
                        &:nth-child(12)
                            padding-left 5px
                            padding-right 20px
                    &:first-child
                        background-color #edeff2;
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