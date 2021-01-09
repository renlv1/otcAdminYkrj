<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>微信好友信息</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户名</div>
                        <div class="input-wrap"><input v-model.trim="belongUserName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户地址</div>
                        <div class="input-wrap"><input v-model.trim="belongUserAddress" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">选择模式</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{intelligentText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in intelligentFlagEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">是否已删除</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{deleteText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in deleteFlagEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">是否选中</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{selecteText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 3)" v-for="(item,index) in selectedEnum" :key="index">{{item.text}}</el-dropdown-item>
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
                                <div class="li">绑定微信uin</div>
                                <div class="li">微信好友unionId</div>
                                <div class="li">微信好友用户名</div>
                                <div class="li">微信好友昵称</div>
                                <div class="li">微信好友备注名</div>
                                <div class="li">微信好友头像</div>
                                <div class="li">性别</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.belongUserName}}</div>
                                    <div class="li"><span>{{item.belongUserAddress}}</span></div>
                                    <div class="li">{{item.belongUin}}</div>
                                    <div class="li">{{item.uin}}</div>
                                    <div class="li"><span>{{item.userName}}</span></div>
                                    <div class="li"><span>{{item.nickName }}</span></div>
                                    <div class="li">{{item.remarkName}}</div>
                                    <div class="li"><span>{{item.headImgUrl}}</span></div>
                                    <div class="li"><span v-if="item.sex === 1">男</span><span v-else-if="item.sex === 2">女</span><span v-else>-</span></div>
                                    <div class="li operation">
                                        <div class="in-btn" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                        <div class="in-btn" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                    </div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">省份: {{item.province}}</div>
                                            <div class="li">城市: {{item.city}}</div>
                                            <div class="li">辅助标志: {{item.assistFlag}}</div>
                                            <div class="li">选择模式: {{matchType(intelligentFlagEnum,item.intelligentFlag)}}</div>
                                            <div class="li">设置智能回复人的钱包地址: {{item.setUser}}</div>
                                            <div class="li">是否已删除: {{matchType(deleteFlagEnum,item.deleteFlag)}}</div>
                                            <div class="li">是否选中好友: {{matchType(selectedEnum,item.selected)}}</div>
                                            <div class="li">上传头像图片到又拍云标志: {{uploadHeadImgFn(item.uploadHeadImg)}}</div>
                                            <div class="li">上传次数: {{item.uploadCount}} </div>
                                        </div>
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
    </div>
</template>

<script>
  export default {
    data() {
      return {
        id: '',
        belongUserName: '',
        belongUserAddress: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        unfoldActive: 0,
        itemShow: false,
        intelligentText: '',
        intelligentId: '',
        deleteText: '',
        deleteId: '',
        selecteText: '',
        selecteId: '',
        intelligentFlagEnum: [],
        deleteFlagEnum: [],
        selectedEnum: []
      }
    },
    created () {
      this.getType()
    },
    methods: {
      resetFields () {
        this.id = ''
        this.belongUserName = ''
        this.belongUserAddress = ''
        this.intelligentText = '全部'
        this.deleteText = '全部'
        this.selecteText = '全部'
        this.intelligentId = ''
        this.deleteId = ''
        this.selecteId = ''
      },
      uploadHeadImgFn (length) {
        if (length === '-') {
          return '-'
        } else {
          let arr = ['未上传','上传成功','上传失败']
          return arr[length]
        }
      },
      getType () {
        let url = '/public/enumValue?classPackage=klein.WebChatFriend$IntelligentFlagEnum;klein.WebChatFriend$DeleteFlagEnum;klein.WebChatFriend$SelectedEnum&flag=true&state=1'
        this.$fetch.post(`${url}`).then(res => {
          if (res.msg === '成功') {
            let data = res.data.data
            this.intelligentFlagEnum = data.IntelligentFlagEnum
            this.deleteFlagEnum = data.DeleteFlagEnum
            this.selectedEnum = data.SelectedEnum
          }
        })
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.intelligentText = commend.item.text
          this.intelligentId = commend.item.id
        } else if (commend.index === 2) {
          this.deleteText = commend.item.text
          this.deleteId = commend.item.id
        } else if (commend.index === 3) {
          this.selecteText = commend.item.text
          this.selecteId = commend.item.id
        }
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
        let intelligentFlag = parseInt(this.intelligentId, 10) >= 0 ?  parseInt(this.intelligentId, 10) : ''
        let deleteFlag = parseInt(this.deleteId, 10) >= 0 ?  parseInt(this.deleteId, 10) : ''
        let selected = parseInt(this.selecteId, 10) >= 0 ?  parseInt(this.selecteId, 10) : ''
        if (this.total !== 0) {
          this.$fetch.post(`/klein/webChatFriend/queryWebChatFriend`,{
            id: this.id,
            belongUserName: this.belongUserName,
            belongUserAddress: this.belongUserAddress,
            intelligentFlag: intelligentFlag,
            deleteFlag: deleteFlag ,
            selected: selected,
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
                                    flex-direction row
                                    flex-flow wrap
                                    text-align center
                                    padding 0 22px
                                    .li
                                        flex none
                                        width auto
                                        height auto
                                        padding 14px 0
                                        margin-right 100px
                                        text-align left
                                        display: flex
                                        align-items center
                                        @media screen and (max-width 1200px) {
                                            margin-right 30px
                                        }
                                        &:nth-child(1)
                                            padding-left 0
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        width auto
                        text-align center
                        &:nth-child(6)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:3;
                        &:nth-child(7)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:3;
                        &:nth-child(9)
                            span
                                display inline-block
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:3;
                    /*.li
                        width 10%
                        text-align left
                        &:nth-child(1)
                            padding-left 20px
                        &:nth-child(3)
                            flex none
                            width 130px
                            @media screen and (max-width 1200px) {
                                width 90px
                            }
                            span
                                display inline-block
                                width 120px
                                @media screen and (max-width 1200px) {
                                    width 80px
                                }
                        &:nth-child(6)
                            flex none
                            width 130px
                            @media screen and (max-width 1200px) {
                                width 90px
                            }
                            span
                                display inline-block
                                width 130px
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:2;
                                @media screen and (max-width 1200px) {
                                    width 80px
                                }
                        &:nth-child(7)
                            flex none
                            width 80px
                            span
                                display inline-block
                                width 60px
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:2;
                        &:nth-child(9)
                            flex none
                            width 120px
                            @media screen and (max-width 1200px) {
                                width 80px
                            }
                            span
                                display inline-block
                                width 110px
                                word-break: break-all;
                                overflow:hidden;
                                text-overflow:ellipsis;
                                display:-webkit-box;
                                -webkit-box-orient:vertical;
                                -webkit-line-clamp:2;
                                @media screen and (max-width 1200px) {
                                    width 60px
                                }*/
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
