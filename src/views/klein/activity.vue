<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>克莱因瓶活动</span>
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
                        <div class="label">奖励状态</div>
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
                    <div class="search-box btns">
                        <div @click="resetFields()" class="btn black">清空</div>
                        <div class="btn" @click="search">查询</div>
                    </div>
                </div>
                <list-wrap :list="isListNull(list)" :total="total" @change="getData" :pageSize="pageSize">
                    <div class="web-table" :class="{'emptyActive': list.length <= 0}">
                        <div class="center">
                            <div class="thead item-list">
                                <div class="li">ID</div>
                                <div class="li">活动名称</div>
                                <div class="li">活动状态</div>
                                <div class="li">奖励状态</div>
                                <div class="li">下次奖励指针</div>
                                <div class="li">资金池总金额</div>
                                <div class="li">本轮销售总额SIE</div>
                                <div class="li">本轮销售量总年数</div>
                                <div class="li">最后购买人用户名</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.roundName}}</div>
                                    <div class="li">
                                        <span v-if="item.roundState === 1">活动中</span>
                                        <span v-else-if="item.roundState === 2">已结束</span>
                                    </div>
                                    <div class="li">
                                        <span v-if="item.rewardState === 0">待发放</span>
                                        <span v-else-if="item.rewardState === 1">已发放</span>
                                        <span v-else-if="item.rewardState === 2">无需发放</span>
                                    </div>
                                    <div class="li">{{item.rewardIndex}}</div>
                                    <div class="li">{{item.capitalPool}}</div>
                                    <div class="li">{{item.saleTotal}}</div>
                                    <div class="li">{{item.saleAmount}}</div>
                                    <div class="li">{{item.lastUserName}}</div>
                                   <div class="li unfold" >
                                        <span class="in-btn" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></span>
                                        <span class="in-btn" @click="unfold(index)" v-else>展开<i class="icon"></i></span>
                                        <span class="in-btn" @click="defer(item)">延迟结束</span>
                                        <span class="in-btn" @click="advance(item)">提前结束</span>
                                    </div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">最后购买人地址: {{item.lastUserAddress}}</div>
                                            <div class="li">最佳推荐人用户名: {{item.refUserName}}</div>
                                            <div class="li">最佳推荐人地址: {{item.refUserAddress}}</div>
                                            <div class="li">活动开始时间: {{item.startAt}}</div>
                                            <div class="li">活动结束时间: {{item.endAt}}</div>
                                            <div class="li">更新时间: {{item.createAt}}</div>
                                            <div class="li">修改时间: {{item.updateAt}}</div>
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
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        id: '',
        roundName: '',
        roundState: '',
        rewardText: '',
        rewardId: '',
        roundText: '',
        roundId: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        roundStateEnum: [],
        rewardStateEnum: [],
        unfoldActive: 0,
        itemShow: false,
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
        this.id = ''
        this.roundName = ''
        this.roundText = '全部'
        this.rewardText = '全部'
      },
      getType () {
        let url = '/public/enumValue?classPackage=klein.DanRewardRound$RoundStateEnum;klein.DanRewardRound$RewardStateEnum&flag=true&state=1'
        this.$fetch.post(`${url}`).then(res => {
           if (res.msg === '成功') {
             this.roundStateEnum = res.data.data.RoundStateEnum
             this.rewardStateEnum = res.data.data.RewardStateEnum
           }
        })
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        this.loadShow =true
        let rewardId =parseInt(this.rewardId, 10) >= 0 ? parseInt(this.rewardId, 10) : ''
        let roundId =parseInt(this.roundId, 10) >= 0 ? parseInt(this.roundId, 10) : ''
        this.$fetch.post(`/klein/danRewardRound/queryDanRewardRound`,{
          id: this.id,
          roundName: this.roundName,
          roundState: roundId,
          rewardState: rewardId,
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }).then(res => {
           this.loadShow = false
           if (res.msg === "成功") {
             this.list = res.data.page.list
             this.total = res.data.page.totalCount
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
        }
      },
      defer(item){
        Dialog({
          title: '确定延迟20分钟吗？',
          warningShow: true,
          type: 'confirm',
          okFn: () => {
            this.$fetch.post(`/klein/danRewardRound/updateDelayEndAt?id=${item.id}`).then(res => {
               if (res.msg === '成功') {
                 this.search()
                 Dialog({
                   title: '成功延迟20分钟',
                   type: 'alert'
                 })
               }
            })
          }
        })
      },
      advance (item) {
        Dialog({
          title: '确定提前结束？',
          warningShow: true,
          type: 'confirm',
          okFn: () => {
            this.$fetch.post(`/klein/danRewardRound/updateAdvanceEndAt?id=${item.id}`).then(res => {
              if (res.msg === '成功') {
                this.search()
                Dialog({
                  title: '成功提前结束',
                  type: 'alert'
                })
              }
            })
          }
        })
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
                &.emptyActive
                  min-height 60px
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
                                    flex-wrap wrap
                                    text-align center
                                    padding 0 22px
                                    .li
                                        width auto
                                        height auto
                                        padding 14px 0
                                        text-align left
                                        flex none
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
                        &:nth-child(10)
                            flex none
                            width 220px
                            @media screen and (max-width 1200px)
                                width 148px
                    &:first-child
                        background-color #edeff2;
                    .unfold
                        color #2176ff
                        cursor pointer
                        display flex
                        align-items center
                        justify-content flex-start
                        .in-btn
                            border-right 1px solid #DCDFE6
                            padding-right 10px
                            &:nth-child(n+2)
                                padding-left 10px
                                @media screen and (max-width 1200px)
                                    display inline-block
                                    width 48px
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

