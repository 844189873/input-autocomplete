# input-autocomplete组件使用说明
## 1.使用动态数据的情况
> 适用于：根据入输的内容动态从服务器或者本地存储加载数据
### 1.1使用步骤
#### 1).组件导入
在js代码块中导入组件：
```
import inputAutocomplete from '@/components/input-autocomplete/input-autocomplete.vue';
export default {
	components: {
		inputAutocomplete
	},
	data() {
		return {
			name: '',
			//使用静态数据
			//autocompleteStringList: ['汉字行', 'guang zhou', 'hello', '不 行']
		};
	},
	
	methods: {
		//在这里可动态加载提示数据，input-autocomplete有做防抖处理（需设置debounce属性）
		loadAutocompleteData(value) {
			console.log('每次输入经过防抖处理以后都会进到这里');
			return Promise.resolve(['汉字行', 'da tang', '三人行', '大马路', '8哥','我是动态数据']);
		},
		//响应选择事件，接收选中的数据
		selectItemD(data) {
			console.log('收到数据了:', data);
		}
	}

};
```

#### 2).标签使用
在html部分使用标签input-autocomplete，并指定加载提示数据的方法loadData的实现

```
<view class="unit-item">
	<view class="unit-item__label">名称：</view>
	<input-autocomplete
		class="unit-item__input"
		:value="testObj.dname"
		v-model="testObj.dname"
		placeholder="请输入报价单名称"
		highlightColor="#FF0000"
		:loadData="loadAutocompleteData"
		v-on:selectItem="selectItemD"
	></input-autocomplete>
</view>

```
#### 3)完整的示例
```
<template>
	<view class="page">
		<view class="content">
			<view class="unit-title">使用动态数据示例</view>
			<view class="unit-wrapper">
				<view class="unit-item">
					<view class="unit-item__label">名称：</view>
					<input-autocomplete
						class="unit-item__input"
						:value="testObj.dname"
						v-model="testObj.dname"
						placeholder="请输入报价单名称"
						highlightColor="#FF0000"
						:loadData="loadAutocompleteData"
						v-on:selectItem="selectItemD"
					></input-autocomplete>
				</view>
			</view>
			<view class="unit-title">使用静态数据示例</view>
			<view class="unit-wrapper">
				<view class="unit-item">
					<view class="unit-item__label">名称：</view>
					<input-autocomplete
						class="unit-item__input"
						:value="testObj.sname"
						v-model="testObj.sname"
						placeholder="请输入报价单名称"
						highlightColor="#FF0000"
						:stringList="autocompleteStringList"
						v-on:selectItem="selectItemS"
					></input-autocomplete>
				</view>
			</view>
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

			// 正确的做法：在这个方法内写完所有取数据的逻辑
			let url = 'https://www.apiopen.top/journalismApi';
			return uni
				.request({
					url: url
				})
				.then(ret => {
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
				});

			//return Promise.resolve(['汉字行', 'da tang', '三人行', '大马路', '8哥', '我是动态数据']);
		},
		//响应选择事件，接收选中的数据
		selectItemD(data) {
			console.log('收到数据了:', data);
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



```

## 2.使用静态数据的情况
> 适用于：要提示内容是固定某几个值，不用动态的根据输入内容去查询
### 1.1使用步骤
#### 1).组件导入
在js代码块中导入组件：
```
import inputAutocomplete from '@/components/input-autocomplete/input-autocomplete.vue';
export default {
	components: {
		inputAutocomplete
	},
	data() {
		return {
			name: '',
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
		//响应选择事件，接收选中的数据
		selectItemS(data) {
			console.log('收到数据了:', data);
		},
		printLog(){
			console.log(this.testObj);
		}
	}

};
```

#### 2).标签使用
在html部分使用标签input-autocomplete，并指定数据来源stringList

```
<view class="unit-item">
	<view class="unit-item__label">名称：</view>
	<input-autocomplete
		class="unit-item__input"
		:value="testObj.sname"
		v-model="testObj.sname"
		placeholder="请输入报价单名称"
		highlightColor="#FF0000"
		:stringList="autocompleteStringList"
		v-on:selectItem="selectItemS"
	></input-autocomplete>
</view>

```
#### 3)完整的示例
```
<template>
	<view class="page">
		<view class="content">
			<view class="unit-title">使用静态数据示例</view>
			<view class="unit-wrapper">
				<view class="unit-item">
					<view class="unit-item__label">名称：</view>
					<input-autocomplete
						class="unit-item__input"
						:value="testObj.sname"
						v-model="testObj.sname"
						placeholder="请输入报价单名称"
						highlightColor="#FF0000"
						:stringList="autocompleteStringList"
						v-on:selectItem="selectItemS"
					></input-autocomplete>
				</view>
			</view>
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
		//响应选择事件，接收选中的数据
		selectItemS(data) {
			console.log('收到数据了:', data);
		},
		printLog(){
			console.log(this.testObj);
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




```

## 3.其它说明
此组件修改自str-autocomplete，向str-autocomplete作者致谢。
本组件主要做了以下几方面的改造：
* 修改了str-autocomplete中的的字符匹配方式，去掉了提示结果中的高亮显示
* 增加汉语全拼和简拼的提示功能
* 增加对动态数据的支持，可从本地存储或服务器中实时读取数据
* 增加读取提示数据时的防抖功能，对于有性能要求的场景可设置防抖时间

# 版本升级日志
## v1.0.5
* 支持自定义数据对象
* 结果列表增加选择事件selectItem，用于接收所选中条目携带的数据
## v1.0.4
* 修复组件会修改外部数据导致告警的问题
* 优化代码
* 完善使用说明

## v1.0.3
* 修复初始数据不能正常显示问题

## v1.0.2
* 修复app环境下，输入内容不能双向绑定问题

## v1.0.1
* 实现汉语简拼、全拼自动提示
* 实现动态加载提示数据
