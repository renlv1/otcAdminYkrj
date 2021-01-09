<template>
    <div class="c-page media-wrap mar-right">
        <div class="wrap">
            <div class="crumb">
                <span>APP发送详细信息</span>
            </div>
            <div class="main-content">
                <div class="search">
                    <div class="search-box">
                        <div class="label">ID</div>
                        <div class="input-wrap"><input v-model="id" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">接受消息用户</div>
                        <div class="input-wrap"><input v-model.trim="receiveAddress" type="text"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">套利id</div>
                        <div class="input-wrap"><input v-model="arbitrageid" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">主表id</div>
                        <div class="input-wrap"><input v-model="recordId" type="number"></div>
                    </div>
                    <div class="search-box">
                        <div class="label">信息类型</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{typeText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 1)" v-for="(item,index) in typeEnum" :key="index">{{item.text}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                        </div>
                    </div>
                    <div class="search-box">
                        <div class="label">状态</div>
                        <div class="input-wrap dropdown-wrap">
                            <el-dropdown @command="commandChange">
                                <span class="el-dropdown-link">
                                  {{statusText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                                </span>
                                <el-dropdown-menu class="menu" slot="dropdown">
                                    <el-dropdown-item :command="composeValue(item, 2)" v-for="(item,index) in statusEnum" :key="index">{{item.text}}</el-dropdown-item>
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
                                <div class="li">接受消息用户</div>
                                <div class="li">套利id</div>
                                <div class="li">主表id</div>
                                <div class="li">推送消息内容</div>
                                <div class="li">信息类型</div>
                                <div class="li">状态</div>
                                <div class="li">sdk返回数据</div>
                                <div class="li">操作</div>
                            </div>
                            <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                                <div class="flex-item">
                                    <div class="li">{{item.id}}</div>
                                    <div class="li">{{item.receiveAddress}}</div>
                                    <div class="li">{{item.arbitrageid}}</div>
                                    <div class="li">{{item.recordId}}</div>
                                    <div class="li moreContent" @click="contentText(item.content,1)">{{item.content}}</div>
                                    <div class="li">{{matchType(typeEnum,item.type)}}</div>
                                    <div class="li">{{matchType(statusEnum,item.status)}}</div>
                                    <div class="li moreContent" @click="contentText(item.sdkreturndata,2)">{{item.sdkreturndata}}</div>
                                    <div class="li unfold" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                                    <div class="li unfold" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                                </div>
                                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                                    <div class="info-box" v-if="unfoldActive === index && itemShow">
                                        <div class="item-flex-item">
                                            <div class="li" @click="contentText(item.remark,3)">备注: {{item.remark}}</div>
                                            <div class="li">创建时间: {{item.updateTimeStr}}</div>
                                            <div class="li">更新时间: {{item.updatetime}}</div>
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
        itemShow: false,
        unfoldActive: 0,
        id: '',
        receiveAddress: '',
        arbitrageid: '',
        recordId: '',
        statusText: '全部',
        typeText: '全部',
        pageIndex: 1,
        pageSize: 10,
        statusId: '',
        typeEnum: [],
        statusEnum: [],
        total: 0,
        loadShow: false,
        list: []
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
      contentText (itemText,index) {
        if (itemText) {
          let title = ''
          if (index === 1) {
            title = '推送消息内容'
          } else if (index === 2) {
            title = 'sdk返回数据'
          } else {
            title = '备注'
          }
          Dialog ({
            title: title,
            content: itemText,
            type: 'confirm'
          })
        }
      },
      getType () {
        this.$fetch.get(`/public/enumValue?classPackage=message.AppSendMessageDetail$TypeEnum;message.AppSendMessageDetail$StatusEnum&flag=true&state=1`).then((res) => {
          if (res.msg === '成功') {
            this.typeEnum = res.data.data.TypeEnum
            this.statusEnum = res.data.data.StatusEnum
          }
        })
      },
      statusFn (length) {
        let statusText = ''
        this.statusEnum.forEach(item => {
          if (parseInt(item.id) === parseInt(length)) {
            statusText = item.text
          }
        })
        return statusText
      },
      typeFn (length) {
        let statusText = ''
        this.typeEnum.forEach(item => {
          if (parseInt(item.id) === parseInt(length)) {
            statusText = item.text
          }
        })
        return statusText
      },
      commandChange (commend) {
        if (commend.index === 1) {
          this.typeText = commend.item.text
          this.typeId = commend.item.id
        } else if (commend.index === 2) {
          this.statusText = commend.item.text
          this.statusId = commend.item.id
        }
      },
      search () {
        this.total = 1
        this.getData(1,10)
      },
      getData (pageIndex,pageSize) {
        if (parseInt(this.total) !== 0) {
          this.loadShow = true
          this.$fetch.post(`system/appSendDetailInfo/queryAppSendDetailMessageList`,{
            id: this.id,
            receiveAddress: this.receiveAddress,
            arbitrageid: this.arbitrageid,
            recordId: this.recordId,
            type: this.typeId === 'selected' ? '' : this.typeId,
            status: this.statusId === 'selected' ? '' : this.statusId,
            pageIndex: pageIndex || this.pageIndex,
            pageSize: pageSize || this.pageSize,
          }).then((res) => {
            this.loadShow = false
            if (res.msg === '成功') {
              this.list = res.data.page.list
              this.total = res.data.page.totalCount
            }
          })
        }
      },
      resetFields () {
        this.id= ''
        this.receiveAddress = ''
        this.arbitrageid = ''
        this.statusId = '',
        this.recordId = ''
        this.statusText = '全部'
        this.typeText = '全部'
      }
    },
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
    .g-dialog-wrap
        width 90% !important
    .wrap
        background-color: @white;
        .crumb
            padding-bottom 22px;
            margin-bottom 0;
        .main-content
            width 100%;
            background-color #fff;
            padding 0px 40px 0px 24px;
            min-height calc(100vh - 147px);
            .web-table
                padding-top 0
                .item-list
                    width 100%
                    height 80px
                    line-height 80px
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
                            height 80px
                            line-height 80px
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
                                    padding 0 20px
                                    flex-direction row
                                    flex-flow wrap
                                    .li
                                        flex none
                                        width auto
                                        line-height 20px
                                        padding 12px 0
                                        text-align left
                                        margin-right 100px
                                        &:nth-child(1)
                                            width 300px
                                            padding 0
                                            margin 10px 0
                                            margin-right 100px
                                            word-break: break-all;
                                            overflow:hidden; // 超出的文本隐藏
                                            text-overflow:ellipsis;
                                            display:-webkit-box; // 将对象作为弹性伸缩盒子模型显示。
                                            -webkit-box-orient:vertical;  //从上到下垂直排列子元素（设置伸缩盒子的子元素排列方式）
                                            -webkit-line-clamp:3; // 结合上面两个属性，表示显示的行数。
                                            cursor pointer
                                            &:hover
                                                text-decoration underline
                                                color #2176ff
                    &:nth-child(odd)
                        background-color #F8F8FA
                    .li
                        flex 1
                        line-height 15px
                        text-align center 
                        width auto
                        &:nth-child(1)
                            flex none
                            width 80px
                        &:nth-child(5)
                            word-break: break-all;
                            overflow:hidden; // 超出的文本隐藏
                            text-overflow:ellipsis;
                            display:-webkit-box; // 将对象作为弹性伸缩盒子模型显示。
                            -webkit-box-orient:vertical;  //从上到下垂直排列子元素（设置伸缩盒子的子元素排列方式）
                            -webkit-line-clamp:2; // 结合上面两个属性，表示显示的行数。
                        &:nth-child(8)
                            word-break: break-all;
                            overflow:hidden; // 超出的文本隐藏
                            text-overflow:ellipsis;
                            display:-webkit-box; // 将对象作为弹性伸缩盒子模型显示。
                            -webkit-box-orient:vertical;  //从上到下垂直排列子元素（设置伸缩盒子的子元素排列方式）
                            -webkit-line-clamp:2; // 结合上面两个属性，表示显示的行数。
                    .moreContent
                        cursor pointer
                        &:hover
                            text-decoration underline
                            color #2176ff
                    &:first-child
                        background-color #edeff2 !important;
                        color @text !important;
                    .unfold
                        color #2176ff
                        cursor pointer
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

