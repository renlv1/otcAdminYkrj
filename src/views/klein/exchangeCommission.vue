<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>新币种委托</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户名</div>
                        <div class="input-wrap"><input v-model.trim="entrustName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">交易类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{etypeText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in etypeEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">订单状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{stateText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in stateEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">成交状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{finishText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 3)" v-for="(item,index) in finishStateEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">价格类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{priceText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 4)" v-for="(item,index) in priceTypeEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">是否参与活动</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{flagText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 5)" v-for="(item,index) in flagEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <!--data组件-->
                    <div class="block">
                        <span class="demonstration">时间</span>
                        <el-date-picker
                                v-model="startDateStr"
                                :picker-options="pickerBeginDateBefore"
                                type="datetime"
                                placeholder="开始时间"
                                clearable="clearable"
                                clear-icon="none"
                                default-time="00:00:00">
                        </el-date-picker>
                        <span class="hr">-</span>
                        <el-date-picker
                                v-model="endDateStr"
                                :picker-options="pickerBeginDateAfter"
                                type="datetime"
                                clearable="clearable"
                                clear-icon="none"
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
                                <div class="li">用戶名</div>
                                <div class="li">用户地址</div>
                                <div class="li">买入币种</div>
                                <div class="li">卖出币种</div>
                                <div class="li">交易类型</div>
                                <div class="li">委托价格</div>
                                <div class="li">委托数量</div>
                                <div class="li">委托总金额</div>
                                <div class="li">订单状态</div>
                                <div class="li">成交数量</div>
                                <div class="li">成交总金额</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.entrustName}}</div>
                                    <div class="li"><span>{{item.entrustAddress}}</span></div>
                                    <div class="li">{{item.buyCurrency}}</div>
                                    <div class="li">{{item.sellCurrency}}</div>
                                    <div class="li">{{etypeFn(item.etype)}}</div>
                                    <div class="li">{{item.price}}</div>
                                    <div class="li">{{item.amount}}</div>
                                    <div class="li">{{item.cashAmount}}</div>
                                    <div class="li">{{matchType(stateEnum,item.state)}}</div>
                                    <div class="li">{{item.tradeAmount}}</div>
                                    <div class="li">{{item.tradeCashAmount}}</div>
                                    <div class="li operation">
                                        <div class="in-btn" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                        <div class="in-btn" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                    </div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">成交状态: {{matchType(finishStateEnum,item.finishState)}}</div>
                                            <div class="li">成交手续费: {{item.tradeFee}}</div>
                                            <div class="li">价格类型: {{matchType(priceTypeEnum,item.priceType)}}</div>
                                            <div class="li">成交时间: {{item.finishAt}}</div>
                                            <div class="li">是否参与活动: {{matchType(flagEnum,item.flag)}}</div>
                                            <div class="li">帐变处理标识: {{processFlagFn(item.processFlag)}}</div>
                                            <div class="li">创建时间: {{item.createAt}}</div>
                                            <div class="li">更新时间: {{item.createAtStr}}</div>
                                            <div class="li">克莱因瓶SIE交易场次: {{item.actNo}}</div>
                                            <div class="li">系统跟单id: {{item.copyId}}</div>
                                            <div class="li">app订单id: {{item.tradeid}}</div>
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
  /*import Dialog from '@/components/dialog/dialog'*/
  export default {
    data() {
      return {
        id: '',
        entrustName: '',
        currencyText: '',
        currencyId: '',
        etypeText: '',
        etypeId: '',
        stateText: '',
        stateId: '',
        finishText: '',
        finishId: '',
        priceText: '',
        priceId: '',
        flagText: '',
        flagId: '',
        startDateStr: '',
        endDateStr: '',
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        etypeEnum: [],
        stateEnum: [],
        finishStateEnum: [],
        priceTypeEnum: [],
        flagEnum: [],
        currencyEnum: [],
        unfoldActive: 0,
        itemShow: false,
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
        this.etypeId = ''
        this.stateId = ''
        this.finishId = ''
        this.priceId = ''
        this.flagId = ''
        this.id = ''
        this.entrustName = ''
        this.etypeText = '全部'
        this.stateText = '全部'
        this.finishText = '全部'
        this.priceText = '全部'
        this.flagText = '全部'
        this.startDateStr = ''
        this.endDateStr = ''
      },
      getType () {
        let url = '/public/enumValue?classPackage=klein.CoinentrustRecord$TypeEnum;klein.CoinentrustRecord$CurrencyEnum;klein.CoinentrustRecord$StateEnum;klein.CoinentrustRecord$FinishStateEnum;klein.CoinentrustRecord$PriceTypeEnum;klein.CoinentrustRecord$FlagEnum&flag=true&state=1'
        this.$fetch.post(`${url}`).then(res => {
          if (res.msg === '成功') {
            let data = res.data.data
            this.etypeEnum = data.TypeEnum
            this.stateEnum = data.StateEnum
            this.finishStateEnum = data.FinishStateEnum
            this.priceTypeEnum = data.PriceTypeEnum
            this.flagEnum = data.FlagEnum
            /*this.currencyEnum.push(all,...data.CurrencyEnum)*/
          }
        })
      },
      //帐变处理标识  1 成功 2：冻结失败 3：撮合退回失败 4：取消委托失败
      processFlagFn (length) {
        if (String(length) === '-') {
          return '-'
        } else {
          let arr = ['成功','冻结失败','撮合退回失败','取消委托失败']
          return arr[length - 1] 
        }
      },
      etypeFn (length) {
        if (String(length) === '-') {
          return '-'
        } else {
          let arr = ['买入','卖出']
          return arr[length - 1]
        }
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.etypeText = commend.item.text
          this.etypeId = commend.item.id
        } else if (commend.index === 2) {
          this.stateText = commend.item.text
          this.stateId = commend.item.id
        } else if (commend.index === 3) {
          this.finishText = commend.item.text
          this.finishId = commend.item.id
        } else if (commend.index === 4) {
          this.priceText = commend.item.text
          this.priceId = commend.item.id
        } else if (commend.index === 5) {
          this.flagText = commend.item.text
          this.flagId = commend.item.id
        }
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        this.loadShow = true
        let etype = this.etypeId === 'selected' ? '' : this.etypeId
        let state = this.stateId === 'selected' ? '': this.stateId
        let finishState = this.finishId === 'selected' ? '' : this.finishId
        let priceType = this.priceId === 'selected' ?  '' : this.priceId
        let flag = this.flagId === 'selected' ? '' : this.flagId
        /*let currency = parseInt(this.currencyId, 10) >= 0 ?  parseInt(this.currencyId, 10) : ''*/
        let objData ={
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }
        if (this.id) objData.id = this.id
        if (this.entrustName) objData.entrustName = this.entrustName
        objData.etype = etype
        objData.state = state
        objData.finishState = finishState
        objData.priceType = priceType
        objData.flag = flag
        /*if (currency) objData.currency = currency*/
        if (this.startDateStr) objData.startDateStr = this.$changeDate(this.startDateStr, '-', 8)
        if (this.endDateStr) objData.endDateStr = this.$changeDate(this.endDateStr, '-', 8)
        if (this.total !== 0) {
          this.$fetch.post(`/klein/coinentrust/queryCoinentrustRecordList`,objData).then(res => {
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
                            background-color #fff
                            .info-box
                                width 100%
                                height 100%
                                .item-flex-item
                                    display flex
                                    align-items center
                                    justify-content flex-start
                                    text-align center
                                    flex-direction row
                                    flex-flow wrap
                                    padding 0 22px
                                    .li
                                        flex none
                                        width auto
                                        height auto
                                        padding 14px 0
                                        text-align left
                                        margin-right 100px
                                        @media screen and (max-width 1200px) {
                                            margin-right 50px
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
