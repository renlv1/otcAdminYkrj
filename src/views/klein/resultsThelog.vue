<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>乐透业绩累积日志</span>
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
                        <div class="label">累积类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{totalTypeText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in totalTypeEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">是否大小边切换</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{changeText  || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in  changeBigEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">业绩来源参赛id</div>
                        <div class="input-wrap"><input v-model="groupPlayerId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">业绩来源活动id</div>
                        <div class="input-wrap"><input v-model="groupRoundId" type="number"></div>
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
                        <table class="center">
                            <div class="thead item-list">
                                 <div class="li">ID</div>
                                 <div class="li">用户名</div>
                                 <div class="li">用户地址</div>
                                 <div class="li">推荐人用户名</div>
                                 <div class="li">推荐人地址</div>
                                 <div class="li">累计类型</div>
                                 <div class="li">是否大小边切换</div>
                                 <div class="li">累计业绩</div>
                                 <div class="li">累计前业绩</div>
                                 <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                 <div class="flex-item">
                                     <div class="li">{{item.id}}</div>
                                     <div class="li">{{item.userName}}</div>
                                     <div class="li"><span>{{item.userAddress}}</span></div>
                                     <div class="li">{{item.refName}}</div>
                                     <div class="li"><span>{{item.refAddress}}</span></div>
                                     <div class="li">{{matchType(totalTypeEnum,item.totalType)}}</div>
                                     <div class="li"><span v-if="item.changeBig === 0">否</span><span v-else-if="item.changeBig === 1">是</span></div>
                                     <div class="li">{{item.totalAmount}}</div>
                                     <div class="li">{{item.beforeTotalAmount}}</div>
                                     <div class="li unfold" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                     <div class="li unfold" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                 </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li">累计后业绩: {{item.afterTotalAmount}}</div>
                                            <div class="li">业绩相关币种: {{item.totalCurrency}}</div>
                                            <div class="li">业绩来源参赛id: {{item.groupPlayerId}}</div>
                                            <div class="li">业绩来源活动id: {{item.groupRoundId}}</div>
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
                        </table>
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
        userName: '',
        userAddress: '',
        refName: '',
        refAddress: '',
        list: [],
        total: 0,
        pageIndex: 1,
        pageSize: 10,
        loadShow: false,
        changeText: '',
        totalTypeText: '',
        changeId: '',
        totalTypeId: '',
        groupRoundId: '',
        groupPlayerId: '',
        changeBigEnum: [],
        totalTypeEnum: [],
        startDateStr: '',
        endDateStr: '',
        unfoldActive: 0,
        itemShow: false,
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
        this.id = ''
        this.userName = ''
        this.userAddress = ''
        this.refName = ''
        this.refAddress = ''
        this.roundId = ''
        this.changeText = '全部'
        this.totalTypeText = '全部'
        this.startDateStr = ''
        this.endDateStr = ''
        this.changeId = ''
        this.totalTypeId = ''
        this.groupPlayerId = ''
        this.groupRoundId = ''
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
        this.$fetch.post(`/public/enumValue?classPackage=klein.LottoGroupLog$TotalTypeEnum;klein.LottoGroupLog$ChangeBigEnum;&flag=true&state=1`).then(res => {
          if (res.msg === '成功') {
           let data = res.data.data
           this.changeBigEnum = data.ChangeBigEnum
           this.totalTypeEnum = data.TotalTypeEnum
          }
        })
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.totalTypeText = commend.item.text
          this.totalTypeId = commend.item.id
        } else if (commend === 2) {
          this.changeText = commend.item.text
          this.changeId = commend.item.id
        }
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData(pageIndex,pageSize){
        this.loadShow = true
        let objData ={
          pageIndex: pageIndex || this.pageIndex,
          pageSize: pageSize || this.pageSize,
        }
        if (this.id) objData.id = this.id
        if (this.userName) objData.userName = this.userName
        if (this.userAddress) objData.userAddress = this.userAddress
        if (this.refName) objData.refName = this.refName
        if (this.refAddress) objData.refAddress = this.refAddress
        objData.totalType = this.totalTypeId === 'selected' ? '' : this.totalTypeId
        objData.changeBig = this.changeId === 'selected' ? '' : this.changeId
        if (this.groupPlayerId) objData.groupPlayerId = this.groupPlayerId
        if (this.groupRoundId) objData.groupRoundId = this.groupRoundId
        if (this.startDateStr) objData.startDateStr = this.$changeDate(this.startDateStr,'-', 8)
        if (this.endDateStr) objData.endDateStr = this.$changeDate(this.endDateStr,'-', 8)
        this.$fetch.post(`/klein/lottoGroupLog/queryLottoGroupLogList`,objData).then(res => {
          this.loadShow = false
          if (res.msg === '成功') {
            this.list = res.data.page.list
            this.total = res.data.page.totalCount
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
                                        width auto
                                        height auto
                                        padding 14px 0
                                        text-align left
                                        flex none
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
                    width 114px
                .input-wrap
                    flex 1
            .label
                padding-right 16px
                font-size 14px
                line-height 14px
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
