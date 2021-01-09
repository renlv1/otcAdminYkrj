<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>克莱因瓶奖励记录</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户名</div>
                        <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">用户地址</div>
                        <div class="input-wrap"><input v-model.trim="userAddress" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">推荐人用户名</div>
                        <div class="input-wrap"><input v-model.trim="refName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">推荐人地址</div>
                        <div class="input-wrap"><input v-model.trim="refAddress" type="text"></div>
                    </div>
                    <div class="search-box">
                    <div class="label">奖励状态</div>
                    <div class="input-wrap dropdown-wrap">
                        <el-dropdown @command="commandChange">
                            <span class="el-dropdown-link">
                              {{refStateText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                            </span>
                            <el-dropdown-menu class="menu" slot="dropdown">
                                <el-dropdown-item :command="composeValue(item,1)" v-for="(item,index) in refStateEnum" :key="index">{{item.text}}</el-dropdown-item>
                            </el-dropdown-menu>
                        </el-dropdown>
                    </div>
                </div>
                    <div class="search-box">
                        <div class="label">循环奖励状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{roundRewardText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in roundRewardStateEnum" :key="index">{{item.text}}</el-dropdown-item>
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
                                <div class="li"><div class="text">推荐人用户名</div></div>
                                <div class="li"><div class="text">推荐人地址</div></div>
                                <div class="li">奖励状态</div>
                                <div class="li">循环奖励状态</div>
                                <div class="li">奖金金额</div>
                                <div class="li">奖励来源ID</div>
                                <div class="li">奖励来源参与者ID</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.userName}}</div>
                                    <div class="li"><div class="text">{{item.userAddress}}</div></div>
                                    <div class="li"><div class="text">{{item.refName}}</div></div>
                                    <div class="li"><div class="text">{{item.refAddress}}</div></div>
                                    <div class="li">{{matchType(refStateEnum,item.rewardType)}}</div>
                                    <div class="li">{{matchType(roundRewardStateEnum,item.rewardState)}}</div>
                                    <div class="li">{{item.rewardAmount}}</div>
                                    <div class="li">{{item.rewardRoundId}}</div>
                                    <div class="li">{{item.rewardPlayerId}}</div>
                                    <div class="li unfold" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                    <div class="li unfold" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">部分奖励所属参与id: {{item.yourPlayerId}}</div>
                                            <div class="li">奖励支付: {{item.rewardPayId}}</div>
                                            <div class="li">备注: {{item.remark}}</div>
                                            <div class="li">支付时间: {{item.payAt}}</div>
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
        userName: '',
        userAddress: '',
        refName: '',
        refAddress: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        itemShow: false,
        refStateText: '',
        refStateId: '',
        roundRewardText: '',
        roundRewardId: '',
        unfoldActive: 0,
        refStateEnum: [],
        roundRewardStateEnum: [],
      }
    },
    created () {
      this.getType()
    },
    methods: {
      resetFields () {
        this.id = ''
        this.userName = ''
        this.userAddress = ''
        this.refName = ''
        this.refAddress = ''
        this.refStateText = ''
        this.roundRewardText = ''
        this.roundRewardId = ''
        this.refStateId = ''
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
      getType () {
        let url = '/public/enumValue?classPackage=klein.DanRewardRecord$RewardTypeEnum;klein.DanRewardRecord$RewardStateEnum&flag=true&state=1'
        this.$fetch.post(`${url}`).then((res) => {
          if (res.msg === '成功') {
           this.roundRewardStateEnum = res.data.data.RewardStateEnum
           this.refStateEnum = res.data.data.RewardTypeEnum
          }
        })
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        this.loadShow = true
        let roundRewardId = parseInt(this.roundRewardId, 10) >= 0 ? parseInt(this.roundRewardId, 10) : ''
        let refStateId = parseInt(this.refStateId, 10) >= 0 ? parseInt(this.refStateId, 10) : ''
        if (this.total !== 0) {
          this.$fetch.post(`/klein/danRewardRecord/queryDanRewardRecord`,{
            id: this.id,
            userName: this.userName,
            userAddress: this.userAddress,
            refName: this.refName,
            refAddress: this.refAddress,
            rewardType: refStateId,
            rewardState: roundRewardId,
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
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.refStateText = commend.item.text
          this.refStateId = commend.item.id
        } else if (commend.index === 2) {
          this.roundRewardText = commend.item.text
          this.roundRewardId = commend.item.id
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
                            .info-box
                                width 100%
                                height 100%
                                .item-flex-item
                                    display flex
                                    align-items center
                                    flex-wrap wrap
                                    justify-content flex-start
                                    text-align center
                                    padding 0 22px
                                    .li
                                        width auto
                                        height auto
                                        padding 14px 0
                                        text-align left
                                        flex none
                                        margin-right 100px
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

