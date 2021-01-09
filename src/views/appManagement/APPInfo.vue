<template>
  <div class="c-page wrap mar-right">
    <div class="crumb">
      <span>app端信息</span>
    </div>
    <div class="main-content">
      <div class="search">
        <div class="search-box">
          <div class="label">用户地址：</div>
          <div class="input-wrap"><input v-model.trim="valueID" type="text"></div>
        </div>
        <div class="f1"></div>
        <div class="btns">
          <div @click="resetFields()" class="btn black">清空</div>
          <div class="btn" @click="search">查询</div>
        </div>
      </div>
      <list-wrap :list="isListNull(list)" :total="total" @change="getData">
        <div class="web-table">
          <table class="center">
            <tr class="thead">
              <td>用户名</td>
              <td>用户地址</td>
              <td>昵称</td>
              <td>SIE持有量</td>
              <td>购买达尔文业绩</td>
              <td>克莱因交易所购买业绩</td>
            </tr>
            <tr v-for="(item, index) in list" :key="index" class="tbody-tr">
              <td>{{item.userName}}</td>
              <!--<td @click="contentText(item.userAddress)">{{item.userAddress}}</td>-->
              <td>{{item.userAddress}}</td>
              <td class="url-img">
                  <img v-if="item.headImg && item.headImg !== '-'" :src="item.headImg">
<!--                  <i class="null" v-else>-</i>-->
                  <span class="text">{{item.nickName}}</span>
              </td>
              <td>{{item.buySieAchievement || '0'}}</td>
              <td>{{item.buyDarwinAchievement === '-' ? '0' : item.buyDarwinAchievement}}</td>
              <td>{{item.buyKleinTradeAchievement || '0'}}</td>
            </tr>
            <div style="z-index: 9999" class="web-loading1" v-show="loadShow">
              <img src="~@img/loading.gif" />
            </div>
          </table>
        </div>
      </list-wrap>
    </div>
  </div>
</template>

<script>
  import { Message } from 'element-ui'
  import Dialog from '@/components/dialog/dialog'
  export default {
    data() {
      return {
        valueID: '',
        list: [],
        total: 0,
        pageIndex: 1,
        loadShow: false,
        addressDialog: false,
        key: '',
        allList: [],
        pageSize: 10,
        showUserAddress: ''
      }
    },
    created() {
    },
    methods: {
      contentText (itemText) {
        if (itemText) {
          Dialog ({
            title: '用户地址',
            content: itemText,
            type: 'confirm'
          })
        }
      },
      btnDelete(item) {
        this.key = item.key
        this.showDialog = true
      },
      paySure() {
          this.addressDialog = false
      },
      resetFields() {
        this.valueID = ''
      },
      getData(pageIndex,pageSize) {
        if(pageSize) {
          this.pageSize = pageSize
        }
        this.loadShow = true
        this.$fetch.post('/app/queryYeJi', {
          address: this.valueID, // id
          pageIndex: pageIndex || this.pageIndex,
          pageSize: this.pageSize,
        }).then((res) => {
          this.loadShow = false
          if(res.data.page) {
            this.list = res.data.page.list
            this.total = res.data.page.totalCount
          }
        }).catch(err => {
          this.loadShow = false
        })
      },
      getTurn(page) {
        this.list = this.allList.slice((page -1)  * this.pageSize, page * this.pageSize)
      },
      search() {
        if (this.valueID === '') {
          Message.error('请输入用户地址!')
        } else {
          this.total = 1 // hook listWrap组件的loading
          this.getData(1,10)
        }
      },
    },
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    .main-container
        padding 34px 20px 0 40px;
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
                padding-top 0 !important
                td + td::before
                    display none
                & tr
                    &:first-child
                        background-color #edeff2 !important;
                        color #121212;
    .search
        position relative
        display flex
        flex-direction row
        flex-wrap wrap
        justify-content flex-start
        border-bottom none
        .search-box
            width 318px
            height 34px
            line-height 34px
            display flex
            align-items center
            &:nth-child(6)
                width 22%
                .label
                    width auto !important
                    padding-right 4px
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
