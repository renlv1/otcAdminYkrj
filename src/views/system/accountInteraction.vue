<template>
  <div class="c-page media-wrap mar-right">
    <div class="wrap">
      <div class="crumb">
        <span>加速赏金单记录</span>
      </div>
      <div class="main-content">
        <div class="search">
          <div class="search-box">
            <div class="label">ID</div>
            <div class="input-wrap"><input v-model="id" type="number"></div>
          </div>
          <div class="search-box">
            <div class="label">调用用户</div>
            <div class="input-wrap"><input v-model.trim="userName" type="text"></div>
          </div>
          <div class="search-box">
            <div class="label">调用用户地址</div>
            <div class="input-wrap"><input v-model.trim="userAddress" type="text"></div>
          </div>
          <div class="search-box">
            <div class="label">交易id</div>
            <div class="input-wrap"><input v-model="tradeid" type="number"></div>
          </div>
          <!--<div class="search-box">
            <div class="label">类</div>
            <div class="input-wrap"><input v-model="category" type="text"></div>
          </div>-->
          <!--<div class="search-box">
            <div class="label">方法</div>
            <div class="input-wrap"><input v-model="methodFn" type="text"></div>
          </div>-->
          <div class="search-box">
            <div class="label">支付类型</div>
            <div class="input-wrap dropdown-wrap">
              <el-dropdown @command="handleCommand">
                <span class="el-dropdown-link">
                  {{giveTypeText || '全部'}}<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu class="menu" slot="dropdown">
                  <el-dropdown-item :command="item" v-for="(item,index) in typeArr" :key="index">{{item.text}}</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
          </div>
          <div class="search-box btns">
            <div @click="resetClear()" class="btn black">清空</div>
            <div class="btn" @click="search">查询</div>
          </div>
        </div>
        <list-wrap :list="isListNull(list)" :total="total" @change="getData" :pageSize="pageSize">
          <div class="web-table">
            <div class="center">
              <div class="thead item-list">
                <span class="li">ID</span>
                <span class="li">调用用户名</span>
                <span class="li">调用用户地址</span>
                <span class="li">支付类型</span>
                <!--<span class="li">类</span>
                <span class="li">方法</span>-->
                <span class="li">返回参数</span>
                <span class="li">操作账户金额</span>
                <span class="li">状态</span>
                <div class="li">交易id</div>
                <div class="li">操作</div>
              </div>
              <div class="item-list table-list" v-for="(item, index) in list" :key="index">
                <div class="flex-item">
                  <div class="li">{{item.id}} </div>
                  <div class="li">{{item.userName}}</div>
                  <div class="li"><span>{{item.userAddress}}</span></div>
                  <!--<div class="li">{{JSON.parse(item.paramStr).paymentType}}</div>-->
                  <div class="li">{{matchType(typeArr,item.type)}}</div>
                  <!--<div class="li">{{item.className || '-'}}</div>
                  <div class="li">{{item.method || '-'}}</div>-->
                  <div class="li moreContent" @click="contentText(item.returnStr)"><span>{{item.returnStr}}</span></div>
                  <!--<div class="li">{{JSON.parse(item.returnStr).code}}</div>-->
                  <div class="li">{{item.total}}</div>
                  <div class="li"><span>{{item.status}}</span></div>
                  <div class="li"><span>{{item.tradeid}}</span></div>
                  <div class="li unfold" v-if="unfoldActive === index && itemShow === true" @click="unfold(index +'2')">收起<i class="icon" :class="{'active':  unfoldActive === index && itemShow === true}"></i></div>
                  <div class="li unfold" v-else @click="unfold(index)">展开<i class="icon"></i></div>
                </div>
                <div class="on-the-details" :class="{'active': unfoldActive === index && itemShow}">
                  <div class="info-box" v-if="unfoldActive === index && itemShow">
                    <div class="item-flex-item">
                      <div class="li">冻结前的余额: {{item.beforeBalance}}</div>
                      <div class="li">冻结前冻结余额: {{item.beforeBalance}}</div>
                      <div class="li">调接口参数: {{item.paramStr}}</div>
                      <div class="li">调接口: {{item.url}}</div>
                      <div class="li">备注: {{item.remark}}</div>
                    </div>
                    <div class="item-flex-item">
                      <div class="li">创建时间: {{$changeDate(item.createTime,'-',1)}}</div>
                      <div class="li">更新时间: {{$changeDate(item.updateTime,'-',1)}}</div>
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

<script type="text/ecmascript-6">
    import Dialog from '@/components/dialog/dialog'
	export default {
		data() {
			return {
				list: [],
				total: 0,
				id: '',
				userName: '',
				userAddress: '',
				tradeid: '',
				category: '',
				methodFn: '',
				giveTypeText: '',
				giveType: '',
				pageIndex: 1,
                pageSize: 10,
				typeArr: [],
				itemShow: false,
				loadShow: false,
				unfoldActive: 0,
				unfoldText: '展开',
      }
		},
		created () {
			this.getType()
		},
		methods: {
			resetClear () {
				this.id = ''
				this.userName = ''
				this.userAddress = ''
				this.tradeid = ''
				this.category = ''
				this.methodFn = ''
				this.giveTypeText = '全部'
				this.giveType = ''
			},
          /**/
          contentText (itemText) {
            if (itemText) {
              Dialog ({
                title: '返回参数',
                content: itemText,
                type: 'confirm'
              })
            }
          },
			getType () {
				this.$fetch.post(`/public/enumValue?classPackage=user.Transaction$PaymentTypeEnum&flag=true&state=1`).then((res) => {
					if (res.msg === '成功') {
						this.typeArr = res.data.data.PaymentTypeEnum
					}
				})
			},
          typeFn (length) {
            let statusText = ''
            this.typeArr.forEach(item => {
              if (parseInt(item.id) === parseInt(length)) {
                statusText = item.text
              }
            })
            return statusText
          },
			handleCommand(command) {
				this.giveTypeText = command.text
				this.giveType = command.id
			},
			search () {
				this.total = 1 // hook listWrap组件的loading
				this.getData(1,10)
			},
			getData (pageIndex,pageSize) {
				if (parseInt(this.total) !== 0) {
					this.loadShow = true
					this.$fetch.post(`/system/appLog/queryAppInterfaceLog`,{
						id: this.id,
						userName: this.userName,
						userAddress: this.userAddress,
                        tradeid: this.tradeid,
						className: this.category,
						method: this.methodFn,
						type: this.giveType === 'selected' ? '' : this.giveType,
						pageIndex: pageIndex || this.pageIndex,
						pageSize: pageSize || this.pageSize,
					}).then((res) => {
					  if (res.msg === '成功') {
                        this.loadShow = false
                        this.list = res.data.page.list
                        this.total = res.data.page.totalCount
                      }
					})
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
			}
		},
	}
</script>

<style scoped lang="stylus">
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
                  flex-direction row
                  flex-flow wrap
                  padding 0 22px
                  .li
                    width 100%
                    flex none
                    height auto
                    line-height 14px
                    padding 14px 0
                    text-align left
                    margin-right 50px
                    @media screen and (max-width 1200px) {
                        margin-right 30px
                    }
          &:nth-child(odd)
            background-color #F8F8FA
          &:first-child
            background-color #edeff2 !important;
            color @text !important;
          .li
            flex 1
            line-height 15px
          .li
                text-align center
                &:nth-child(5)
                    word-break: break-all;
                    overflow:hidden;
                    text-overflow:ellipsis;
                    display:-webkit-box;
                    -webkit-box-orient:vertical;
                    -webkit-line-clamp:3;
          .moreContent
              cursor pointer
              &:hover
                  text-decoration underline
                  color #2176ff
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
          position absolute
          top 0
          left 0
          .el-dropdown-link
            height 100%
            line-height 14px
          span
            width 100%
            display flex
            align-items center
            justify-content space-between
            padding 0 6px
            .el-icon--right
              margin-left 12px !important
    .btns
      justify-content flex-start
      .btn
        cursor pointer
</style>
