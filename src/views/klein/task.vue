<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>乐透分红奖励任务</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">任务所属活动ID</div>
                        <div class="input-wrap"><input v-model="roundId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">任务类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{jobText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in jobTypeEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">奖励任务状态</div>
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
                    <div class="block">
                        <span class="demonstration">时间</span>
                        <el-date-picker
                                clear-icon="none"
                                v-model="startDateStr"
                                :picker-options="pickerBeginDateBefore"
                                type="datetime"
                                placeholder="开始时间"
                                clearable="clearable"
                                default-time="00:00:00">
                        </el-date-picker>
                        <span class="hr">-</span>
                        <el-date-picker
                                clear-icon="none"
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
                        <table class="center">
                            <tr class="thead">
                                <td width="6%">ID</td>
                                <td>任务类型</td>
                                <td>奖励任务状态</td>
                                <td>累计奖励金额</td>
                                <td>奖励所属小时数</td>
                                <td>上级奖励所属小时数</td>
                                <td>任务所属活动id</td>
                                <td>备注</td>
                                <td>创建时间</td>
                                <td>更新时间</td>
                            </tr>
                            <tr v-for="(item, index) in list" :key="index">
                                <td>{{item.id}}</td>
                                <td>{{matchType(jobTypeEnum,item.jobType)}}</td>
                                <td>{{matchType(rewardStateEnum,item.rewardState)}}</td>
                                <td>{{item.rewardTotal}}</td>
                                <td>{{item.rewardHour}}</td>
                                <td>{{item.rewardRefHour}}</td>
                                <td>{{item.roundId}}</td>
                                <td>{{item.remark}}</td>
                                <td>{{item.createAt}}</td>
                                <td>{{item.updateAt}}</td>
                            </tr>
                            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
                                <img src="~@img/loading.gif" />
                            </div>
                        </table>
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
        loadShow: false,
        jobTypeEnum: [],
        rewardStateEnum: [],
        rewardText: '',
        rewardId: '',
        jobText: '',
        jobId: '',
        roundId: '',
        startDateStr: '',
        endDateStr: '',
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
      resetFields () {
        this.jobId = ''
        this.rewardId = ''
        this.id = ''
        this.roundId = ''
        this.jobText = ''
        this.rewardText = ''
        this.startDateStr = ''
        this.endDateStr = ''
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.jobText = commend.item.text
          this.jobId = commend.item.id
        } else if (commend.index === 2) {
          this.rewardText = commend.item.text
          this.rewardId = commend.item.id
        }
      },
      getType () {
        this.$fetch.post(`/public/enumValue?classPackage=klein.LottoBonusJob$JobTypeEnum;klein.LottoBonusJob$RewardStateEnum;&flag=true&state=1`).then(res => {
          if (res.msg === '成功') {
            let data = res.data.data
            this.jobTypeEnum = data.JobTypeEnum
            this.rewardStateEnum = data.RewardStateEnum
          }
        })
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
        if (this.roundId) objData.roundId = this.roundId
        objData.jobType = this.jobId === 'selected' ? '' : this.jobId
        objData.rewardState = this.rewardId === 'selected' ? '' : this.rewardId
        if (this.startDateStr) objData.startDateStr = this.$changeDate(this.startDateStr, '-', 8)
        if (this.endDateStr) objData.endDateStr = this.$changeDate(this.endDateStr, '-', 8)
        if (this.total !== 0) {
          this.$fetch.post(`/klein/lottoBonusJob/queryLottoBonusJobList`,objData).then(res => {
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
    @import "../../assets/css/var.styl"
    .main-container
        padding 24px 20px 0 40px;
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
                td + td::before
                    display none
                & tr
                    &:first-child
                        background-color #edeff2 !important;
                        color @text !important;
                td
                  &:nth-child(1)
                     @media screen and (max-width 1200px)
                         width 60px
    .search
        position relative
        display flex
        flex-direction row
        flex-wrap wrap
        justify-content flex-start
        border-bottom none
        .search-box
            width 322px
            height 34px
            line-height 34px
            margin: 4px 0 15px;
            display flex
            align-items center
            &:nth-child(5)
                .input-wrap
                    margin-right 0
            &:nth-child(7)
                .input-wrap
                    flex none
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
            margin-bottom 15px
            .demonstration
              margin-right 16px
            .el-input
                width auto !important
                height 34px !important
                .el-input__inner
                    width auto !important
                    height 34px !important
                    flex 1 !important
        .btns
            justify-content flex-start
            @media screen and (max-width 1054px) {
                justify-content flex-start
            }
            .btn
                cursor pointer
</style>
