<template>
	<view>
		<view class="goods-list">
		  <block v-for="(goods, i) in goodsList" :key="i" @click="gotoDetail(item)">
			<my-goods :goods="goods"></my-goods>
		  </block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				queryObj: {
				      // 查询关键词
				      query: '',
				      // 商品分类Id
				      cid: '',
				      // 页码值
				      pagenum: 1,
				      // 每页显示多少条数据
				      pagesize: 10
				    },
				// 商品列表的数据
				goodsList: [],
				// 总数量，用来实现分页
				total: 0,
				isLoading: false
			};
		},
		onLoad(options){
			this.queryObj.query = options.query || ''
			this.queryObj.cid = options.cid || ''
			this.getGoodsList()
		},
		methods:{
			  async getGoodsList(cb) {
				  
				if (this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')
				this.isLoading = true
			    // 发起请求
			    const { data: res } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
				cb && cb()
			    if (res.meta.status !== 200) return uni.$showMsg()
			    // 为数据赋值
				
				this.goodsList = [...this.goodsList, ...res.message.goods]
			    this.total = res.message.total
				this.isLoading = false
			  },
			  onReachBottom() {
				if(this.isLoading){
					return
				}
			    // 让页码值自增 +1
			    this.queryObj.pagenum += 1
			    // 重新获取列表数据
			    this.getGoodsList()
			  },
			  onPullDownRefresh() {
			    // 1. 重置关键数据
			    this.queryObj.pagenum = 1
			    this.total = 0
			    this.isloading = false
			    this.goodsList = []
			  
			    // 2. 重新发起请求
			    this.getGoodsList(() => uni.stopPullDownRefresh())
			  },
			  gotoDetail(item) {
			    uni.navigateTo({
			      url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
			    })
			  }
		},
	}
</script>

<style lang="scss">
	

</style>
