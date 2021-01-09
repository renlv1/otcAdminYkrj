<template>
    <div class="mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>基金赎回</span>
                <div class="new-add" @click="transfer">确认</div>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">用户名</div>
                        <div class="input-wrap"><input v-model="transferUserName" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">金额</div>
                        <div class="input-wrap"><input v-model="amount" type="number"></div>
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
        userName: '',
        amount: ''
      }
    },
    methods: {
      transfer () {
        this.$fetch.post(`/pk/fund/fundRedeem`,{
          userName: this.userName,
          amount : this.amount,
        }).then((res) => {
          if (res.msg === '转账成功') {
            Dialog({
              title: res.msg,
              okFn: () => {
                this.$router.push('/pk/fdinfo')
              }
            })
          }
        }).catch((err) => {
          Dialog({
            title: err.msg
          })
        })
      }
    },
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    .main-container
        padding 24px 20px 0 40px;
    .wrap
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
                color #fff
                text-align center
                cursor pointer
                background-color #2176ff
                border-radius 2px
                transition all .2s linear
                &:hover
                    background-color #fff !important
                    border 1px solid #2176ff
                    color #121212
        .main-content
            width 100%;
            height calc(100vh - 186px)
            background-color #fff;
            padding 24px 40px 0px 24px;
    .search
        position relative
        border-bottom none
        .search-box
            width auto
            height 34px
            line-height 34px
            display flex
            align-items center
            justify-content flex-start
            margin 0
            margin-bottom 20px
            .label
                width 102px
                padding-right 60px
                font-size 14px
            .input-wrap
                width 300px
                height 34px
                line-height 34px
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
            justify-content flex-start
        .btns
            justify-content flex-end
            .btn
                cursor pointer
</style>


