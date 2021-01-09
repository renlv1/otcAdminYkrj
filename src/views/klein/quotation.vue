<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>新币种行情</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <!--备注:data组件-->
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
                                <div class="li">货币对币种1</div>
                                <div class="li">货币对币种2</div>
                                <div class="li">时间类型</div>
                                <div class="li">交易量</div>
                                <div class="li">成交笔数</div>
                                <div class="li">开盘价格</div>
                                <div class="li">最高价格</div>
                                <div class="li">最低价格</div>
                                <div class="li">收盘价格</div>
                                <div class="li">平均价格</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.firstCurrency}}</div>
                                    <div class="li">{{item.secondCurrency}}</div>
                                    <div class="li">{{dateType(item.dateType)}}</div>
                                    <div class="li">{{item.volume}}</div>
                                    <div class="li">{{item.tcount}}</div>
                                    <div class="li">{{item.openPrice}}</div>
                                    <div class="li">{{item.highPrice}}</div>
                                    <div class="li">{{item.lowPrice}}</div>
                                    <div class="li">{{item.closePrice}}</div>
                                    <div class="li">{{item.vwapPrice}}</div>
                                    <div class="li unfold" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                    <div class="li unfold" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">开始时间: {{item.startTime}}</div>
                                            <div class="li">结束时间: {{item.closeTime}}</div>
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
        startDateStr: '',
        endDateStr: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        itemShow: false,
        unfoldActive: 0,
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
    methods: {
      resetFields () {
        this.id = ''
        this.startDateStr = ''
        this.endDateStr = ''
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
        let objData ={
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }
        if (this.id) objData.id = this.id
        if (this.startDateStr) objData.startDateStr = this.$changeDate(this.startDateStr, '-', 8)
        if (this.endDateStr) objData.endDateStr = this.$changeDate(this.endDateStr, '-', 8)
        if (this.total !== 0) {
          this.$fetch.post(`/klein/cointradeVolume/queryCointradeVolumeList`,objData).then(res => {
            this.loadShow = false
             if (res.msg === '成功') {
               this.list = res.data.page.list
               this.total = res.data.page.totalCount
             }
          })
        }
      },
      //0:最新 1:分 2:15分钟 3:30分钟 4:1小时 5:24小时 6:月 7:季度(3个月) 8:年
      dateType (length) {
        if (length === '-') {
          return '-'
        } else {
          let arr = ['最新','分','15分钟','30分钟','1小时','24小时','月','季度(3个月)','年']
          return arr[length] 
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    @import "../../assets/css/var.less";
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
                                        //min-height 64px
                                        // line-height 64px
                                        //padding 13px 0
                                        margin: 15px 50px 15px 0
                                        width auto
                                        text-align left
                                        flex none
                                        &:nth-child(1)
                                            padding-left 0
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        width auto
                        text-align center
                    /*.li
                        width 10%
                        text-align left
                        &:nth-child(1)
                            padding-left 20px
                            flex none
                            width 60px
                        &:nth-child(2)
                            flex none
                            width 80px
                            text-align left
                            @media screen and (max-width 1200px) {
                                width 50px
                            }
                        &:nth-child(3)
                            flex none
                            width 80px
                            text-align left
                            @media screen and (max-width 1200px) {
                                width 50px
                            }
                        &:nth-child(n+4)
                           flex 1
                            @media screen and (max-width 1200px) {
                                flex none
                                width 38px
                            }
                        &:nth-child(10)
                            flex none
                            width 90px
                            text-align left*/
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
        margin-bottom 15px
        .search-box
            width 318px
            height 34px
            line-height 34px
            margin: 0;
            display flex
            align-items center
            margin-right 4px !important
            margin-bottom 20px
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
        .block
            width auto
            margin 0 auto
            margin-left 0
            margin-bottom 20px
        .btns
            flex none
            width auto
            justify-content flex-start
            margin-bottom 0
            .btn
                cursor pointer
        div
          /*@media screen and (max-width 1400px) {
              width: 33% !important;
          }*/

</style>
