<template>
	<view>
		<view class="cu-bar bg-white search ">
			<view class="search-form round">
				<text class="cuIcon-search"></text>
				<input type="text" placeholder="输入物品名称" v-model="resName" confirm-type="search"></input>
			</view>
			<view class="action">
				<button class="cu-btn bg-gradual-green shadow-blur round" @tap="_searchResource()">搜索</button>
			</view>
		</view>
		<view class="margin-top" v-if="noData==false">
			<view class="cu-list menu-avatar " v-for="(item,index) in resourceList" :key="index">
				<view class="cu-item">
					<view class="content content-left">
						<view class="text-grey">
							<!-- <text class="cuIcon-notification text-cut text-green margin-right-xs"></text> -->
							<text class="ellip">{{item.resName}}-{{item.goodsTypeName}}</text>
						</view>
						<view class="text-gray text-sm flex">
							<view class="text-cut">
								库存：{{item.stock}}
							</view>
						</view>
					</view>
				</view>
			</view>
			<view class="load-more" @click="_loadResourceList()">加载更多</view>
		</view>
		<view v-else>
			<no-data-page></no-data-page>
		</view>
	</view>
</template>

<script>
	
	import {queryMyResourceStoreInfo} from '../../api/resource/resource.js'
	import noDataPage from '@/components/no-data-page/no-data-page.vue'
	export default {
		data() {
			return {
				orderImg: this.java110Constant.url.baseUrl + 'img/order.png',
				resourceList: [],
				resName: '',
				noData:false,
				page: 1,
			}
		},
		components: {
			noDataPage
		},
		onLoad() {
		},
		onShow() {
			this.resourceList = [];
			this.page = 1;
			this._loadResourceList();
		},
		methods: {
			
			checkAuth: function(pid){
				return this.java110Context.hasPrivilege(pid);
			},
			
			_loadResourceList: function() {
				let _that = this;
				let _objData = {
					resName: _that.resName,
					page: _that.page,
					row: 10,
					communityId: _that.java110Context.getCurrentCommunity().communityId
				};
				queryMyResourceStoreInfo(this, _objData)
					.then(function(res) {
						console.log('here is ', res);
						if (res.code != 0) {
							uni.showToast({
								icon: 'none',
								title: res.msg
							});
							return;
						}
						if(res.data.length <= 0){
							uni.showToast({
								title: '已全部加载'
							})
							return;
						}
						
						let _data = res.data;
						
						_that.resourceList = _that.resourceList.concat(_data);
						_that.page ++;
						
						if(_that.resourceList.length < 1){
							_that.noData = true;
							return ;
						}
					});
			},
			_searchResource: function() {
				this.resourceList = [];
				this.page = 1;
				this._loadResourceList();
			},
		}
	}
</script>

<style>
	.cu-list.menu-avatar>.cu-item .content-left {
		left: 30upx;
	}

	.cu-list+.cu-list {
		margin-top: 20upx;
	}
</style>
