<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>基金信息</span>
                <div class="new-add" @click="redemption">基金赎回</div>
            </div>
            <div class="main-content">
                <div class="search verNote">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">基金id</div>
                        <div class="input-wrap"><input v-model="fundId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户名</div>
                        <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户地址</div>
                        <div class="input-wrap"><input v-model.trim="address" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{statusText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in statusEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{categoryText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in categoryEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">累计状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{flagText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 3)" v-for="(item,index) in flagEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <!--data组件-->
                    <div class="block">
                        <span class="demonstration">时间</span>
                        <el-date-picker
                                clear-icon="none"
                                clearable="clearable"
                                v-model="startDateStr"
                                :picker-options="pickerBeginDateBefore"
                                type="datetime"
                                placeholder="开始时间"
                                default-time="00:00:00">
                        </el-date-picker>
                        <span class="hr">-</span>
                        <el-date-picker
                                clear-icon="none"
                                clearable="clearable"
                                v-model="endDateStr"
                                :picker-options="pickerBeginDateAfter"
                                type="datetime"
                                placeholder="结束时间"
                                default-time="23:59:59">
                        </el-date-picker>
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
                                <div class="li">基金id</div>
                                <div class="li">用户名</div>
                                <div class="li">用户地址</div>
                                <div class="li">金额</div>
                                <div class="li">汇率</div>
                                <div class="li">解锁时间</div>
                                <div class="li">解锁汇率</div>
                                <div class="li">周期</div>
                                <div class="li">类型</div>
                                <div class="li">累计状态</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.fundId}}</div>
                                    <div class="li">{{item.userName}}</div>
                                    <div class="li"><span>{{item.address}}</span></div>
                                    <div class="li">{{item.amount}}</div>
                                    <div class="li">{{item.rate}}</div>
                                    <div class="li">{{item.endDate}}</div>
                                    <div class="li">{{item.endRate}}</div>
                                    <div class="li">{{item.period}}</div>
                                    <div class="li">{{matchType(categoryEnum,item.category)}}</div>
                                    <div class="li">{{matchType(flagEnum,item.flag)}}</div>
                                    <div class="li unfold" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                    <div class="li unfold" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">状态: {{matchType(statusEnum,item.status)}}</div>
                                            <div class="li">备注: {{item.remark}}</div>
                                            <div class="li">创建时间: {{item.createDate}}</div>
                                            <div class="li">更新时间: {{item.updateDate}}</div>
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
            <div class="dialog-modal" v-show="modalShow">
                <div class="content-box">
                    <div class="item-li">
                        <div class="title">基金赎回</div>
                        <div class="input-text"><div class="close" @click="closeModal"></div></div>
                    </div>
                    <div class="item-li">
                        <div class="title">用户名</div>
                        <div class="input-text"><input placeholder="用户名" v-model="transferUserName" type="text"></div>
                    </div>
                    <div class="item-li">
                        <div class="title">金额</div>
                        <div class="input-text"><input placeholder="金额" v-model="amount" type="number"></div>
                    </div>
                    <div class="item-li">
                        <div class="title"></div>
                        <div class="input-text">
                            <div class="button" @click="affirmDredge()">确认</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        modalShow: false,
        transferUserName: '',
        amount: '',
        id: '',
        fundId: '',
        userName: '',
        address: '',
        statusText: '',
        categoryText: '',
        flagId: '',
        categoryId: '',
        flagText: '',
        statusId: '',
        statusEnum: [],
        categoryEnum: [],
        itemShow: false,
        flagEnum: [],
        startDateStr: '',
        endDateStr: '',
        unfoldActive: 0,
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        pageSize: 10,
        pickerBeginDateBefore: {
          disabledDate: (time) => {
            let beginDateVal = this.endDateStr
            if (beginDateVal) {
              return time.getTime() > beginDateVal
            }
          }
        },
        pickerBeginDateAfter: {
          disabledDate: (time) => {
            let beginDateVal = this.startDateStr
            if (beginDateVal) {
              return time.getTime() < beginDateVal
            }
          }
        },
      }
    },
    created () {
      this.getType()
    },
    methods: {
      closeModal () {
        this.modalShow = false
        this.transferUserName = ''
        this.amount = ''
      },
      redemption () {
        this.modalShow = true
      },
      affirmDredge () {
        this.$fetch.post(`/pk/fund/fundRedeem`,{
          userName: this.transferUserName,
          amount: this.amount,
        }).then((res) => {
          this.modalShow = false
          if (res.msg === '转账成功') {
            Dialog({
              title: res.msg,
              okFn: () => {
                this.$router.push('/pk/fdinfo')
              }
            })
          }
          this.transferUserName = ''
          this.amount = ''
        })
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
      resetFields () {
        this.id = ''
        this.fundId = ''
        this.userName = ''
        this.address = ''
        this.statusText = '全部'
        this.statusId = ''
        this.categoryText = '全部'
        this.categoryId = ''
        this.flagText = '全部'
        this.flagId = ''
        this.startDateStr = ''
        this.endDateStr = ''
      },
      getType () {
        this.$fetch.post(`/public/enumValue?classPackage=pk.FundRecord$StatusEnum;pk.FundRecord$CategoryEnum;pk.FundRecord$FlagEnum; User$mobileEnum;User$googlePwdEnum; &flag=true&state=1`).then((res) => {
           if (res.msg === '成功') {
            this.statusEnum = res.data.data.StatusEnum
            this.categoryEnum = res.data.data.CategoryEnum
            this.flagEnum =  res.data.data.FlagEnum
           }
        })
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.statusText = commend.item.text
          this.statusId = commend.item.id
        } else if (commend.index === 2) {
          this.categoryText = commend.item.text
          this.categoryId = commend.item.id
        } else if (commend.index === 3) {
          this.flagText = commend.item.text
          this.flagId = commend.item.id
        }
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData: function (pageIndex, pageSize) {
        this.loadShow = true
        let status = this.statusId === 'selected' ? '' : this.statusId
        let category = this.categoryId === 'selected' ? '' : this.categoryId
        let flag = this.flagId === 'selected' ? '' : this.flagId
        let objData = {
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }
        if (this.id) objData.id = this.id
        if (this.fundId) objData.fundId = this.fundId
        if (this.userName) objData.userName = this.userName
        if (this.address) objData.address = this.address
        objData.status = status
        objData.category = category
        objData.flag = flag
        if (this.startDateStr) objData.startAtStr = this.$changeDate(this.startDateStr, '-', 8)
        if (this.endDateStr) objData.endAtStr = this.$changeDate(this.endDateStr, '-', 8)
        if (parseInt(this.total) !== 0) {
          this.$fetch.post(`/pk/fund/queryFundList`, objData).then((res) => {
            this.loadShow = false
            if (res.msg === '成功') {
              this.list = res.data.page.list
              this.total = res.data.page.totalCount
            }
          })
        }
      },
    },
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    @import '../../assets/css/var.styl';
    .wrap
        background-color: @white;
        .crumb
            display flex;
            align-items center;
            justify-content space-between
            padding-bottom 22px;
            margin-bottom 0
            .new-add
                width 90px
                height 34px
                line-height 34px
                font-size 14px
                color #2176ff
                text-align center
                cursor pointer
                border 1px solid #2176ff
                border-radius 2px
                transition all .2s linear
                &:hover
                    background-color #fff !important
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
                            justify-content center
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
                                    justify-content center
                                    text-align center
                                    height 64px
                                    line-height 64px
                                    background-color #fff
                                    .li
                                        flex none
                                        width auto
                                        text-align left
                                        padding-right 80px
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        width auto
                    .li
                        text-align center
                        padding-left 20px
                        &:nth-child(1)
                            @media screen and (max-width 1200px){
                                flex none
                                width 60px
                            }
                        &:nth-child(n+2)
                           padding-left 0
                        &:nth-child(4)
                            flex none
                            width 160px
                            @media screen and (max-width 1200px){
                                width 110px
                            }
                            span
                               display inline-block
                               width 130px
                               @media screen and (max-width 1200px){
                                    width 100px
                               }
                        &:nth-child(6)
                            @media screen and (max-width 1200px){
                                flex none
                                width 40px
                            }
                        &:nth-child(7)
                            flex none
                            width 100px
                            margin-right 10px
                            @media screen and (max-width 1200px){
                                width 70px
                                margin-right 0
                            }
                        &:nth-child(9)
                            @media screen and (max-width 1200px){
                                flex none
                                width 30px
                            }
                    &:first-child
                        background-color #edeff2 !important;
                        color @text !important;
                    .unfold
                        color #2176ff
                        cursor pointer
                        display flex
                        align-items center
                        justify-content center
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
        .dialog-modal
            position fixed
            width 100%
            height 100%
            top 0
            left 0
            right 0
            z-index 1000
            background-color rgba(0,0,0,.2)
            font-size 14px
            color #121212
            .content-box
                width 450px
                height 260px
                background-color #fff
                border-radius 4px
                position absolute
                top 50%
                left 50%
                transform translate(-50%,-50%)
                padding 22px 33px
                z-index 9999
                .item-li
                    width 100%
                    line-height 34px
                    display flex
                    align-items center
                    justify-content space-between
                    margin-bottom 30px
                    &:nth-child(1)
                        .title
                            font-size 16px
                        .input-text
                            width: 16px
                    &:last-child
                        .input-text
                            height 100%
                            display flex
                            align-items center
                            justify-content flex-end
                            .button
                                width: 80px
                                height: 34px
                                line-height 34px
                                text-align center
                                cursor pointer
                                background-color #2176ff
                                &:nth-child(1)
                                    border 1px solid #e5e8ed
                                    color #fff
                                &:nth-child(2)
                                    background-color #2176ff
                                    color #fff
                                    margin-left 20px
                    .input-text
                        .close
                            width 16px
                            height: 16px
                            background url("~@img/close.svg") no-repeat center
                            background-size contain
                            cursor pointer
                        input
                            display inline-block
                            width 294px
                            height: 34px
                            border 1px solid #e5e8ed
                            border-radius 2px
                            padding 0 10px
                    .input-wrap
                        .el-dropdown-link
                            width: 294px;
                            height: 34px
                            display flex
                            align-items center
                            justify-content space-between
                            padding 0 10px
                            border 1px solid #e5e8ed

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
            &:nth-child(8)
                width auto !important
                .picker-time
                    flex 1
                    display flex
                    align-items center
                    justify-content flex-start
                    .el-input
                        width 47% !important
                        .el-input__inner
                            width 100%!important
                            margin-right 0 !important
                            padding 0 6px !important
                    .hr
                        margin 0 5px
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


