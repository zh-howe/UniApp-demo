<template>
	<view class="serach">
		<view class="content" :style="{'border-radius':radius+'px'}">
			<view class="content-box" :class="{'center':mode === 2}" @click="getFocus">
				<text class="iconfont icon-sousuo9"></text>
				<input  :placeholder="placeholder" @input="inputChange" confirm-type="search" @confirm="triggerConfirm" class="input" :class="{'center':!active && mode === 2}" :focus="isFocus" v-model="inputVal" @focus="focus" @blur="blur"/>
			</view>
			<view v-show="(active&&show&&button === 'inside')||(isDelShow && button === 'inside')" class="serachBtn" @click="search">
				搜索
			</view>
		</view>
		<view  v-if="button === 'outside'" class="button" :class="{'active':show||active}" @click="search">
			<view class="button-item">{{!show?searchName:'搜索'}}</view>
		</view>
	</view>
</template>

<script>
export default {
	props: {
		mode: {
			value: Number,
			default: 1
		},
		placeholder:{
			value: String,
			default: '请输入搜索内容'
		},
		value: {
			type: String,
			default:false
		},
		button: {
			value: String,
			default: 'outside'
		},
		show: {
			value: Boolean,
			default: true
		},
		radius: {
			value: String,
			default: 60
		}
	},
	data() {
		return {
			active: false,
			inputVal: '',
			searchName: '取消',
			isDelShow: false,
			isFocus: false
		};
	},
	methods: {
		triggerConfirm(){
			this.$emit('confirm', false);
		},
		inputChange(event){
			var keyword = event.detail.value;
			this.$emit('input', keyword);
			if (this.inputVal) {
				this.isDelShow = true;
			}
		},
		focus() {
			this.active = true;
			if (this.inputVal) {
				this.isDelShow = true;
			}
		},
		blur() {
			this.isFocus = false;
			if (!this.inputVal) {
				this.active = false;
			}
		},
		clear() {
			uni.hideKeyboard();
			this.isFocus = false;
			this.inputVal = '';
			this.active = false;
			this.$emit('input', '');
			//this.$emit('search', '');
			
		},
		getFocus() {
			this.isFocus = true;
		},
		search() {
			if (!this.inputVal) {
				if(!this.show&&this.searchName == '取消'){
					uni.hideKeyboard();
					this.isFocus = false;
					this.active = false;
					return;
				}
			}
			console.log(this.inputVal); 
			this.$emit('search', this.inputVal?this.inputVal:this.placeholder);
		}
	},
	watch: {
		inputVal(newVal) {
			if (newVal) {
				this.searchName = '搜索';
			} else {
				this.searchName = '取消';
				this.isDelShow = false;
			}
		},
		//HM修改 双向绑定
		value(val) {
			this.inputVal = val;
		},
	}
};
</script>

<style lang="scss" scoped>
.serach {
	display: flex;
	width: 100%;
	box-sizing: border-box;
	font-size: $uni-font-size-base;
	.content {
		display: flex;
		align-items: center;
		width: 100%;
		height: 60upx;
		background: #fff;
		overflow: hidden;
		transition: all 0.2s linear;
		border-radius: 30px;

		.content-box {
			width: 100%;
			display: flex;
			align-items: center;
			&.center {
				justify-content: center;
			}
			.iconfont {
				padding: 0 15upx;
				font-size: 38upx;
				color:#999;
			}
			.input {
				width: 100%;
				max-width: 100%;
				line-height: 60upx;
				height: 60upx;
				transition: all 0.2s linear;
				&.center {
					width: 200upx;
				}
				&.sub {
					// position: absolute;
					width: auto;
					color: grey;
				}
			}
		}
		.serachBtn {
			height: 100%;
			flex-shrink: 0;
			padding: 0 30upx;
			background:linear-gradient(to right,#fc3939,#dd2424);
			line-height: 60upx;
			color: #fff;
			transition: all 0.3s;
		}
	}

	.button {
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
		flex-shrink: 0;
		width: 0;
		transition: all 0.2s linear;
		white-space: nowrap;
		overflow: hidden;
		&.active {
			padding-left: 15upx;
			width: 100upx;
		}
	}
}
// @font-face {font-family:"iconfont";src:url('data:application/x-font-woff;charset=utf-8;base64,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');}
// 
// 
// .icon {
// 	font-family: iconfont;
// 	font-size: 32upx;
// 	font-style: normal;
// 	color: #999;
// }
</style>
