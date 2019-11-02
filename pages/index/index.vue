<template>
	<view class="page">
		<view class="content">
			<view class="unit-title">使用动态数据示例</view>
			<view class="unit-wrapper">
				<view class="unit-item">
					<view class="unit-item__label">名称：</view>
					<input-autocomplete class="unit-item__input" :value="testObj.dname" v-model="testObj.dname" placeholder="请输入报价单名称"
					 highlightColor="#FF0000" :loadData="loadAutocompleteData" v-on:selectItem="selectItemD"
					 :debounce = "1000"></input-autocomplete>
				</view>
			</view>
			<view class="unit-title">使用静态数据示例</view>
			<view class="unit-wrapper">
				<view class="unit-item">
					<view class="unit-item__label">名称：</view>
					<input-autocomplete class="unit-item__input" :value="testObj.sname" v-model="testObj.sname" placeholder="请输入报价单名称"
					 highlightColor="#FF0000" :stringList="autocompleteStringList" v-on:selectItem="selectItemS"></input-autocomplete>
				</view>
			</view>
			
			<view class="unit-wrapper">
			<view class="unit-item">
				<view class="unit-item__label">普通输入框：</view>
				<input type="text" class="unit-item__input" v-model="testObj.remarks" placeholder="请输入备注" />
			</view>
			</view>
			<button @tap="printLog">打印结果</button>
			<button @tap="changeStaticData">改变静态数据</button>
		</view>
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
					sname: '',
					dname: '动态'
				},
				//使用静态数据
				autocompleteStringList: [
					'汉字行',
					'guang zhou',
					{
						//自定义数据对象必须要有text属性
						text: 'hello',
						//其它字段根据业务需要添加
						key: 'hello key'
					},
					'不 行',
					{
						//自定义数据对象必须要有text属性
						text: '我是静态数据',
						//其它字段根据业务需要添加
						id: 'hz'
					}
				]
			};
		},
		methods: {
			loadAutocompleteData(value) {
				console.log('每次输入经过防抖处理以后都会进到这里。');
				console.log('此参数就是输入框的值：', value);
				
				// 【注意】：由于此方法是组件调用进来的，这里的this对象已经不是指向当前页面了
				// 所以无法在这里通过this去取当前页面的数据；
				// 基于同样的原因，也无法通过this去调用当前页的其它方法。
				// 【正确的做法】：在这个方法内写完所有取数据的逻辑，如果需要用输入框的值则取这里的value参数
				
				//可以通过$root访问根父组件，也就是当前页面
				let that = this.$root;
				console.log('访问当前页的数据:',{
					dname:that.testObj.dname,
					sname:that.testObj.sname
				});
				
				let url = 'https://www.apiopen.top/journalismApi';
				return uni
					.request({
						url: url
					})
					.then(ret => {
						var [error, res] = ret;
						console.log(res);
						let data = (((res || {}).data || {}).data || {}).toutiao || [];
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
					});

				//return Promise.resolve(['汉字行', 'da tang', '三人行', '大马路', '8哥', '我是动态数据']);
			},
			//响应选择事件，接收选中的数据
			selectItemD(data) {
				//选择事件
				console.log('收到数据了:', data);
			},
			selectItemS(data) {
				//选择事件
				console.log('收到数据了:', data);
			},
			printLog() {
				console.log(this.testObj);
			}
			,
			changeStaticData() {
				console.log('改变静态数据');
				this.autocompleteStringList= [
					'1汉字行',
					'1change data',
					'1guang zhou',
					{
						//自定义数据对象必须要有text属性
						text: '1hello',
						//其它字段根据业务需要添加
						key: '1hello key'
					},
					'1不 行',
					{
						//自定义数据对象必须要有text属性
						text: '1我是静态数据',
						//其它字段根据业务需要添加
						id: '1hz'
					}
				];
			}
		},

		onLoad: function(option) {
			let that = this;
		}
	};
</script>

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
