<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>新币种成交记录</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">买家用户名</div>
                        <div class="input-wrap"><input v-model.trim="buyName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">卖家用户名</div>
                        <div class="input-wrap"><input v-model.trim="sellName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">买家ID</div>
                        <div class="input-wrap"><input v-model="buyId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">卖家ID</div>
                        <div class="input-wrap"><input v-model="sellId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">交易状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="handleCommand">
                                <span class="el-dropdown-link">
                                  {{statusText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="item" v-for="(item,index) in stateEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <!--data组件-->
                    <div class="block">
                        <span class="demonstration">时间</span>
                        <el-date-picker
                                v-model="startDateStr"
                                type="datetime"
                                placeholder="开始时间"
                                :picker-options="pickerBeginDateBefore"
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
                               <div class="li">买家用户名</div>
                               <div class="li">卖家用户名</div>
                               <div class="li">买家ID</div>
                               <div class="li">卖家ID</div>
                               <div class="li">买单价格</div>
                               <div class="li">卖单价格</div>
                               <div class="li">买入币种</div>
                               <div class="li">卖入币种</div>
                               <div class="li">状态</div>
                               <div class="li">成交价格</div>
                               <div class="li">成交数量</div>
                               <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                   <div class="li">{{item.id}}</div>
                                   <div class="li">{{item.buyName}}</div>
                                   <div class="li">{{item.sellName}}</div>
                                   <div class="li">{{item.buyId}}</div>
                                   <div class="li">{{item.sellId}}</div>
                                   <div class="li">{{item.buyPrice}}</div>
                                   <div class="li">{{item.sellPrice}}</div>
                                   <div class="li">{{item.buyCurrency}}</div>
                                   <div class="li">{{item.sellCurrency}}</div>
                                   <div class="li">
                                       <span v-if="item.state === 1">成功</span>
                                       <span v-else-if="item.state === 2">失败</span>
                                   </div>
                                    <div class="li">{{item.tradePrice}}</div>
                                    <div class="li">{{item.tradeAmount}}</div>
                                    <div class="li operation">
                                        <div class="in-btn" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                        <div class="in-btn" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                    </div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">成交总金额: {{item.tradeTotalPrice}}</div>
                                            <div class="li">成交手续费: {{item.tradeFee}}</div>
                                            <div class="li">创建时间: {{item.createAt}}</div>
                                            <div class="li">更新时间: {{item.updateAt}}</div>
                                            <div class="li">成交时间: {{item.finishAt}}</div>
                                            <div class="li">是否参与活动: {{flagFn(item.flag)}}</div>
                                            <div class="li">撮合处理标识: {{processFlag(item.processFlag)}}</div>
                                            <div class="li">买家用户地址: {{item.buyAddress}}</div>
                                            <div class="li">卖家用户地址: {{item.sellAddress}}</div>
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
        buyName: '',
        sellName: '',
        buyId: '',
        sellId: '',
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        unfoldActive: 0,
        itemShow: false,
        startDateStr: '',
        endDateStr: '',
        statusText: '全部',
        statusId: '',
        stateEnum: [],
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
      resetFields () {
        this.id = ''
        this.buyName = ''
        this.sellName = ''
        this.buyId = ''
        this.sellId = ''
        this.statusText = '全部'
        this.startDateStr = ''
        this.endDateStr = ''
        this.statusId = ''
      },
      flagFn (length) {
        if (length === '-') {
          return '-'
        } else {
          let arr = ['不用处理','未累计','已累计','处理中','处理失败']
          return arr[length] 
        }
      },
      //撮合处理标识  1 成功 2：处理买方失败 3：处理卖方失败 4:买卖都处理失败
      processFlag (length) {
      	if (length === '-') {
          return '-' 
        } else {
          let arr = ['成功','处理买方失败','处理卖方失败','买卖都处理失败']
          return arr[length] 
        }
      },
      getType () {
        this.$fetch.post(`/public/enumValue?classPackage=klein.CointradeRecord$StateEnum&flag=true&state=1`).then(res => {
          if (res.msg === '成功') {
            let data = res.data.data
            this.stateEnum = data.StateEnum
          }
        })
      },
      handleCommand (commend) {
        this.statusText = commend.text
        this.statusId = commend.id
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
        let state = parseInt(this.statusId, 10) >= 0 ?  parseInt(this.statusId, 10) : ''
        let objData ={
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }
        if (this.id) objData.id = this.id
        if (this.buyName) objData.buyName = this.buyName
        if (this.sellName) objData.sellName = this.sellName
        if (this.buyId) objData.buyId = this.buyId
        if (this.sellId) objData.sellId = this.sellId
        if (state) objData.state = state
        if (this.startDateStr) objData.startDateStr = this.$changeDate(this.startDateStr, '-', 8)
        if (this.endDateStr) objData.endDateStr = this.$changeDate(this.endDateStr, '-', 8)
        if (this.total !== 0) {
          this.$fetch.post(`/klein/cointrade/queryCointradeRecordList`,objData).then(res => {
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
                                    text-align center
                                    flex-direction row
                                    flex-flow wrap
                                    padding 0 22px
                                    .li
                                        flex none
                                        width auto
                                        height auto
                                        padding 14px 0
                                        margin-right 100px
                                        text-align left
                                        @media  screen and (max-width 1200px) {
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
                    .li
                        width 10%
                        text-align center
                        &:nth-child(1)
                            padding-left 20px
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
