<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>交易所委托</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="text"></div>
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
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in typeenum" :key="index">{{item.text}}</el-dropdown-item>
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
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in stateenum" :key="index">{{item.text}}</el-dropdown-item>
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
                                    <el-dropdown-item :command="composeValue(item, 3)" v-for="(item,index) in finishstateenum" :key="index">{{item.text}}</el-dropdown-item>
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
                                    <el-dropdown-item :command="composeValue(item, 4)" v-for="(item,index) in pricetypeenum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">委托标识</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{flagText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 5)" v-for="(item,index) in flagenum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
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
                                <div class="li">用户名</div>
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
                                    <div class="li">{{matchType(typeenum,item.etype)}}</div>
                                    <div class="li">{{item.price}}</div>
                                    <div class="li">{{item.amount}}</div>
                                    <div class="li">{{item.cashAmount}}</div>
                                    <div class="li">{{matchType(stateenum,item.state)}}</div>
                                    <div class="li">{{item.tradeAmount}}</div>
                                    <div class="li">{{item.tradeCashAmount}}</div>
                                    <div class="li unfold" v-if="unfoldActive === index && itemShow === true"  @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                    <div class="li unfold" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">成交状态: {{matchType(finishstateenum,item.finishState)}}</div>
                                            <div class="li">成交手续费: {{item.tradeFee}}</div>
                                            <div class="li">价格类型: {{matchType(pricetypeenum,item.priceType)}}</div>
                                            <div class="li">成交时间: {{item.finishAt}}</div>
                                            <div class="li">委托标识: {{matchType(flagenum,item.flag)}}</div>
                                            <div class="li">创建时间: {{item.createAt}}</div>
                                            <div class="li">更新时间: {{item.updateAt}}</div>
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
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        unfoldActive: 0,
        itemShow: false,
        loadShow: false,
        pricetypeenum: [],
        stateenum: [],
        finishstateenum: [],
        typeenum: [],
        flagenum: [],
        currencyenum: [],
        etypeText: '',
        etypeId: '',
        stateText: '',
        stateId: '',
        finishText: '',
        finishId: '',
        priceText: '',
        priceId: '',
        flagText: '',
        startDateStr: '',
        endDateStr: '',
        entrustName: '',
        currencyText: '',
        currencyId: '',
        //        日期控件
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
      getType () {
        let url = '/public/enumValue?classPackage=klein.NewCoinentrustRecord$TypeEnum;klein.NewCoinentrustRecord$StateEnum;klein.NewCoinentrustRecord$FinishStateEnum;klein.NewCoinentrustRecord$PriceTypeEnum;klein.NewCoinentrustRecord$FlagEnum;klein.NewCoinentrustRecord$CurrencyEnum;&flag=true&state=1'
        this.$fetch.post(`${url}`).then(res => {
          if (res.msg === '成功') {
            let typeData =res.data.data
            this.pricetypeenum = typeData.PriceTypeEnum  // 价格类型
            this.stateenum = typeData.StateEnum  // 订单状态
            this.finishstateenum = typeData.FinishStateEnum // 成交状态
            this.typeenum = typeData.TypeEnum  //交易类型
            this.flagenum = typeData.FlagEnum  // 是否参与活动
            this.currencyenum = typeData.CurrencyEnum // 币种
          }
        })
      },
      commandChange (commend) {
        let indexId = commend.index
        if (indexId === 1) {
          this.etypeText = commend.item.text
          this.etypeId = commend.item.id
        } else if (indexId === 2) {
          this.stateText = commend.item.text
          this.stateId = commend.item.id
        } else if (indexId === 3) {
          this.finishText = commend.item.text
          this.finishId = commend.item.id
        } else if (indexId === 4) {
          this.priceText = commend.item.text
          this.priceId = commend.item.id
        } else if (indexId === 5) {
          this.flagText = commend.item.text
          this.flagId = commend.item.id
        }
      },
      resetFields () {
        this.id = ''
        this.entrustName = ''
        this.etypeText = '全部'
        this.stateText = '全部'
        this.finishText = '全部'
        this.priceText = '全部'
        this.flagText = '全部'
        this.currencyText = '全部'
        this.etypeId = ''
        this.stateId = ''
        this.finishId = ''
        this.priceId = ''
        this.flagId = ''
        this.startDateStr = ''
        this.endDateStr = ''
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        this.loadShow = true
        let objData ={
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }
        if (this.id) objData.id = this.id
        if (this.entrustName) objData.entrustName = this.entrustName
        objData.etype = this.etypeId === 'selected' ? '' : this.etypeId
        objData.state = this.stateId === 'selected' ? '' : this.stateId
        objData.finishState = this.finishId === 'selected' ? '' : this.finishId
        objData.priceType = this.priceId === 'selected' ? '' : this.priceId
        objData.flag = this.flagId === 'selected' ? '' : this.flagId
        if (this.startDateStr) objData.startDateStr = this.$changeDate(this.startDateStr, '-', 8)
        if (this.endDateStr) objData.endDateStr = this.$changeDate(this.endDateStr, '-', 8)
        if (this.total !== 0) {
           this.$fetch.post(`/klein/newCoinentrust/queryNewCoinentrustList`,objData).then(res => {
              this.loadShow = false
              if (res.msg === '成功') {
                this.list = res.data.page.list
                this.total = res.data.page.totalCount
              }
           })
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
      processFlagFn (length) {
          if (String(length) === '-') {
            return '-'
          } else {
            let arr = ['成功','冻结失败','退回失败']
            return arr[length -1]
          }
      },
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
                                    text-align center
                                    flex-direction row
                                    flex-flow wrap
                                    padding 0 22px
                                    .li
                                        height auto
                                        padding 14px 0
                                        width auto
                                        flex none
                                        text-align left
                                        margin-right 100px
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
                    &:first-child
                        background-color #edeff2;
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
                   width 100px
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


