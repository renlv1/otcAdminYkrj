<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>乐透活动记录</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">活动名称</div>
                        <div class="input-wrap"><input v-model.trim="roundName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">活动状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{roundText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in roundStateEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">活动奖励状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{rewardText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in rewardStateEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">活动是否接受投注</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{joinText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 3)" v-for="(item,index) in joinStateEnum" :key="index">{{item.text}}</el-dropdown-item>
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
                                clear-icon="none"
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
                <list-wrap :list="isListNull(list)" :total="total" @change="getData">
                    <div class="web-table">
                        <div class="center">
                            <div class="thead item-list">
                                <div class="li">ID</div>
                                <div class="li">活动名称</div>
                                <div class="li">活动状态</div>
                                <div class="li">活动奖励状态</div>
                                <div class="li">活动是否接受投注</div>
                                <div class="li">游戏币种</div>
                                <div class="li">分红池总金额</div>
                                <div class="li">奖金池总金额</div>
                                <div class="li">分红池累计金额</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.roundName}}</div>
                                    <div class="li">{{matchType(roundStateEnum,item.roundState)}}</div>
                                    <div class="li">{{matchType(rewardStateEnum,item.rewardState)}}</div>
                                    <div class="li">{{matchType(joinStateEnum,item.joinState)}}</div>
                                    <div class="li">{{item.rewardCurrency}}</div>
                                    <div class="li">{{item.bonusPool}}</div>
                                    <div class="li">{{item.rewardPool}}</div>
                                    <div class="li">{{item.bonusPoolSum}}</div>
                                    <div class="li operation">
                                        <div class="in-btn" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                        <div class="in-btn" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                    </div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">奖金池累计金额: {{item.rewardPoolSum}}</div>
                                            <div class="li">本轮销售总额: {{item.saleTotal}}</div>
                                            <div class="li">活动开始时间:{{item.startAt}}</div>
                                            <div class="li">活动结束时间: {{item.endAt}}</div>
                                            <div class="li">创建时间: {{item.createAt}}</div>
                                            <div class="li">更新时间: {{item.updateAt}}</div>
                                            <div class="li">备注: {{item.remark}}</div>
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
        roundName: '',
        roundText: '',
        roundId: '',
        rewardText: '',
        rewardId: '',
        joinText: '',
        joinId: '',
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        unfoldActive: 0,
        itemShow: false,
        startDateStr: '',
        endDateStr: '',
        joinStateEnum: [],
        rewardStateEnum: [],
        roundStateEnum: [],
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
        this.roundName = ''
        this.roundText = ''
        this.rewardText = ''
        this.joinText = ''
        this.startDateStr = ''
        this.endDateStr = ''
        this.roundId = ''
        this.rewardId = ''
        this.joinId = ''
        this.roundText = '全部'
        this.rewardText = '全部'
        this.joinText = '全部'
      },
      getType () {
        this.$fetch.post(`/public/enumValue?classPackage=klein.LottoRewardRound$JoinStateEnum;klein.LottoRewardRound$RewardStateEnum;klein.LottoRewardRound$RoundStateEnum;&flag=true&state=1`).then(res => {
           if (res.msg === '成功') {
              let data = res.data.data
              this.roundStateEnum = data.RoundStateEnum
              this.rewardStateEnum = data.RewardStateEnum
              this.joinStateEnum = data.JoinStateEnum
           }
        })
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.roundText = commend.item.text
          this.roundId = commend.item.id
        } else if (commend.index === 2) {
          this.rewardText = commend.item.text
          this.rewardId = commend.item.id
        } else if (commend.index === 3) {
          this.joinText = commend.item.text
          this.joinId = commend.item.id
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
        let objData ={
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }
        if (this.id) objData.id = this.id
        objData.roundName = this.roundName
        objData.roundState = this.roundId === 'selected' ? '' : this.roundId
        objData.rewardState = this.rewardId === 'selected' ? '' : this.rewardId
        objData.joinState = this.joinId === 'selected' ? '' : this.joinId
        if (this.startDateStr) objData.startDateStr = this.$changeDate(this.startDateStr, '-', 8)
        if (this.endDateStr) objData.endDateStr = this.$changeDate(this.endDateStr, '-', 8)
        if (this.total !== 0) {
          this.$fetch.post(`/klein/lottoRewardRound/queryLottoRewardRoundList`,objData).then(res => {
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
                                        height auto
                                        padding 14px 0
                                        width auto
                                        text-align left
                                        line-height 20px
                                        margin-right 100px
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
