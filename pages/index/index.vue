<template>
	<view class="page">
		<view class="content">
			<view class="unit-title">使用动态数据示例</view>
			<view class="unit-wrapper">
				<view class="unit-item">
					<view class="unit-item__label">名称：</view>
					<input-autocomplete class="unit-item__input" :value="testObj.dname" v-model="testObj.dname" placeholder="请输入报价单名称"
					 highlightColor="#FF0000" :loadData="loadAutocompleteData"></input-autocomplete>
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
			<button @tap="printLog">打印结果</button>
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
					sname: '静态',
					dname: '动态'
				},
				//使用静态数据
				autocompleteStringList: ['汉字行', 'guang zhou', 'hello', '不 行', '我是静态数据']
			};
		},
		onLoad() {},
		methods: {
			//在这里可动态加载提示数据，input-autocomplete有做防抖处理（需设置debounce属性）
			loadAutocompleteData(value) {
				console.log(`每次输入经过防抖处理以后都会进到这里。
				注意：由于子组调用到父组的方法，这里拿到的this对象实际上是子组件，
				所这个方法内通过this去调用其它方法是有问题的`);
				
				// 错误的做法：像下面这样子想调用当前组件内的方法是不行的
				// this.$options.methods.printLog();
				
				// 正确的做法：在这个方法内写完所有取数据的逻辑
				let url =
					"https://www.apiopen.top/journalismApi";
				return uni.request({
					url: url
				}).then(
					ret => {
						var [error, res] = ret;
						console.log(res.data);
						let data=((res.data||{}).data||{}).toutiao||[];
						if(data.length<=0){
							return Promise.resolve(['没有数据...']);
						}
						
						let retData=[];
						for(let it of data){
							console.log(it);
							retData.push(it.title);
						}
						return Promise.resolve(retData);
					}
				)
				
				//return Promise.resolve(['汉字行', 'da tang', '三人行', '大马路', '8哥', '我是动态数据']);
			},
			printLog() {
				console.log(this.testObj);
			}
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
