<template>
	<view>
		<view class="userBox">
			<!-- 昵称 -->
			<text class="nickName">{{nickNames}}</text>
			<!-- 头像 -->
			<image class="userIcon" :src="avatarUrl">头像</image>
			<button bindgetuserinfo="getUserInfo" open-type='getUserInfo'>点击授权</button>
		</view>

		<view class="unit-title">使用动态数据示例</view>
		<view class="unit-wrapper">
			<view class="unit-item">
				<view class="unit-item__label">名称：</view>
				<input-autocomplete class="unit-item__input" :value="testObj.dname" v-model="testObj.dname" placeholder="请输入报价单名称"
				 highlightColor="#FF0000" :loadData="loadAutocompleteData" v-on:selectItem="selectItemD"></input-autocomplete>
			</view>
		</view>
		<view class="unit-title">使用静态数据示例</view>
		<view class="unit-wrapper">
			<view class="unit-item">
				<view class="unit-item__label">名称：</view>
				<input-autocomplete class="unit-item__input" :value="testObj.sname" v-model="testObj.sname" placeholder="请输入报价单名称"
				 highlightColor="#FF0000" :stringList="autocompleteStringList"></input-autocomplete>
			</view>
		</view>

		<!-- <view class="unit-item">
     
	 <strAutocomplete
  :stringList="stringList"
  @select="selectOne"
  highlightColor="#FF0000"
  v-model="title"
></strAutocomplete>
	
     
	 
</view> -->
	</view>
</template>


<script>
	import inputAutocomplete from '@/components/input-autocomplete/input-autocomplete.vue';
	export default {
		components: {
			inputAutocomplete
		},
		data() {
			return {
				testObj: {
					sname: '静态',
					dname: '动态'
				},
				//使用静态数据
				autocompleteStringList: ['汉字行', 'guang zhou', ,
					{
						//自定义数据对象必须要有text属性
						text: 'hello',
						//其它字段根据业务需要添加
						key: 'hello key'
					}, '不 行', '我是静态数据'
				],
				nickNames: '匿名用户',
				avatarUrl: '../../../static/logo.png',
				show: '',
				hidden: '',
				title: 'Hello',
				stringList: [
					'apple',
					'string',
					'delicious',
					'everything',
					'somethingWrong',
					'中古国'
				]
			}
		},
		methods: {
			loadAutocompleteData(value) {
				console.log('每次输入经过防抖处理以后都会进到这里。')


				// 正确的做法：在这个方法内写完所有取数据的逻辑
				let url =
					"https://www.apiopen.top/journalismApi";
				return uni.request({
					url: url
				}).then(
					ret => {
						var [error, res] = ret;
						console.log(res.data);
						let data = ((res.data || {}).data || {}).toutiao || [];
						if (data.length <= 0) {
							return Promise.resolve(['没有数据...']);
						}

						let retData = [];
						for (let it of data) {
							// console.log(it);
							retData.push({
								//自定义数据对象必须要有text属性
								text: it.title,
								//其它字段根据业务需要添加
								digest: it.digest
							});
						}
						//console.log(Promise.resolve(retData));
						return Promise.resolve(retData);
					}
				)

				//return Promise.resolve(['汉字行', 'da tang', '三人行', '大马路', '8哥', '我是动态数据']);
			},
			selectItemD(data) {
				console.log('收到数据了:', data);
			},
			selectOne(opt) {
				console.log(opt)
			}
			//在这里可动态加载提示数据，input-autocomplete有做防抖处理（需设置debounce属性）
			// loadAutocompleteData(value) {
			//     console.log('每次输入经过防抖处理以后都会进到这里');
			//     return Promise.resolve(['汉字行', 'da tang', '三人行', '大马路', '8哥','我是动态数据']);
			// }
		},

		onLoad: function(option) {
			let that = this;

			// 			uni.getProvider({
			// 				service: 'oauth',
			// 				success: function(res) {
			// 					console.log(res.provider);
			// 					//支持微信、qq和微博等
			// 					if (~res.provider.indexOf('weixin')) {
			// 						uni.login({
			// 							provider: 'weixin',
			// 							success: function(loginRes) {
			// 								console.log('-------获取openid(unionid)-----');
			// 								console.log(JSON.stringify(loginRes));
			// 								// 获取用户信息
			// 								uni.getUserInfo({
			// 									provider: 'weixin',
			// 									success: function(infoRes) {
			// 										console.log('-------获取微信用户所有-----');
			// 										console.log(JSON.stringify(infoRes.userInfo));
			// 									}
			// 								});
			// 							}
			// 						});
			// 					}
			// 				}
			// 			});
			// 

			//uni.login({
			//	provider: 'weixin',
			//	success: function(loginRes) {
			//	console.log(loginRes);
			// 获取用户信息
			// 					uni.getSetting({
			// success(res) {
			// if (!res.authSetting['scope.userInfo']) {
			// wx.authorize({
			// scope: 'scope.userInfo',
			// success() {
			// // 用户已经同意小程序使用录音功能，后续调用 wx.startRecord 接口不会弹窗询问
			//  uni.getUserInfo({
			//  	provider: 'weixin',
			//  	success: function(infoRes) {
			//  		console.log(infoRes);
			//  		that._data.nickNames = infoRes.userInfo.nickName;
			//  		that._data.avatarUrl = infoRes.userInfo.avatarUrl;
			//  	},  
			//      fail:function(res){  
			//          // 这里res = {"errMsg":"getUserInfo:fail scope unauthorized"}   
			//          console.log('res='+JSON.stringify(res))  
			//      }  
			//  });
			// }
			// })
			// }
			// }
			// });


			//}
			//	});

		}
	}
</script>


<style>
	/* 用户盒子 */
	.userBox {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 20px;
		background: linear-gradient(to top right, #63b8ff 0%, #4876ff 25%, #3a5fcd 100%);
	}

	/* 用户昵称 */
	.nickName {
		color: #ffffff;
	}

	/* 用户头像 */
	.userIcon {
		align-self: flex-end;
		border-radius: 50%;
		overflow: hidden;
		width: 100px;
		height: 100px;
		box-shadow: 0 1px 2px rgba(0, 0, 0, 0.5)
	}
</style>



<style>
	.content {
		text-align: center;
		height: 100%;
	}

	.unit-title {
		font-size: 30upx;
		/* font-style: oblique; */
		color: #fff;
		padding: 16upx 26upx 50upx 26upx;
		padding-bottom: 10upx;
		text-align: left;
	}

	.unit-wrapper {
		padding: 44upx 0;
		margin: 0 30upx;
		border-radius: 32upx;
		margin-bottom: 20upx;
		background-color: #fff;
		color: #000;
	}

	.unit-item {
		display: flex;
		justify-content: flex-end;
		padding-right: 30upx;
		padding-left: 30upx;
		margin-bottom: 30upx;
	}

	.unit-item__header {
		margin-top: 0;
		margin-bottom: 8upx;
		padding: 0upx 8upx;
		display: flex;
		justify-content: space-between;
	}

	.unit-item__label {
		padding-top: 2px;
		text-align: right;
		font-size: 28upx;
		margin: 8upx 0 4upx 16upx;
		min-width: 188upx;
		width: 240upx;
	}

	.unit-item__input {
		text-align: left;
		width: 100%;
		font-size: 28upx;
		margin: 4upx 16upx 0 4upx;
		border: 2upx solid #eaeaea;
		border-radius: 12upx;
		min-height: 52.5upx;
		line-height: 52.5upx;
	}
</style>
