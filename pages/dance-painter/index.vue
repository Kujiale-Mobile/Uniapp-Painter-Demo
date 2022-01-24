<template>
	<view class="page__bg">
		<view class="bg" @click="handleClickBackground" />
		<view class="top">
			<view class="top__block" :style="[hasRevert ? '' : { opacity: '0.3' }]"
				@click="handleTimeMachine('revert')">
				<image src="../../static/images/revert.png" class="top__block__icon" mode="aspectFill" />
				<text>撤销</text>
			</view>
			<view class="top__block" :style="[hasRecover ? '' : { opacity: '0.3' }]"
				@click="handleTimeMachine('recover')">
				<image src="../../static/images/recover.png" class="top__block__icon" mode="aspectFill" />
				<text>恢复</text>
			</view>
			<view class="top__button" @click="handleSaveImage"> 保存 </view>
		</view>
		<painter :customStyle="'margin-top:1vh;'" :customActionStyle="customActionStyle" :dancePalette="dancePalette"
			:palette="palette" :action="action" :clearActionBox="clearActionBox" :disableAction="false"
			@imgOK="handleImgOk" @didShow="handleDidShow" @touchEnd="handleTouchEnd" @viewClicked="handleViewClick"
			@viewUpdate="handleViewUpdate" />

		<view class="page__bottom" :style="[editState === 0 ? { bottom: 0 } : {}]">
			<view class="page__bottom__block" @click="handleEditText">
				<image src="../../static/images/add-text.png" class="page__bottom__block__icon" mode="aspectFill" />
				<text class="page__bottom__block__text">+文字</text>
			</view>
			<view class="page__bottom__block" @click="handleChangeBackground">
				<image src="../../static/images/change-background.png" class="page__bottom__block__icon"
					mode="aspectFill" />
				<text class="page__bottom__block__text">背景</text>
			</view>
		</view>

		<view class="page__bottom--column" :style="[editState===1 ? { bottom: 0 } : {}]">
			<input @input="handleInput" :value="inputValue" placeholder="请输入" class="page__bottom__input" />
			<view class="page__bottom__action">
				<image @click="handleActionCancel" src="../../static/images/close.png"
					class="page__bottom__action__icon" mode="aspectFill" />
				<text class="page__bottom__action__title">{{actionTitle}}</text>
				<image @click="handleActionConfirm" src="../../static/images/right.png"
					class="page__bottom__action__icon" mode="aspectFill" />
			</view>
		</view>

		<view class="page__bottom--column" :style="[editState===2 ? { bottom: 0 } : {}]">
			<view class="page__bottom__color__list">
				<view v-for="color of colors" :key="color" class="page__bottom__color"
					:style="[{ backgroundColor: color, border: selectedColor === color ? '4rpx solid #1A7AF8' : 'none' }]"
					@click="handleSelectColor(color)" />
			</view>
			<view class="page__bottom__action">
				<image @click="handleActionCancel" src="../../static/images/close.png"
					class="page__bottom__action__icon" mode="aspectFill" />
				<text class="page__bottom__action__title">{{actionTitle}}</text>
				<image @click="handleActionConfirm" src="../../static/images/right.png"
					class="page__bottom__action__icon" mode="aspectFill" />
			</view>
		</view>

		<view class="page__bottom" :style="[editState===3 ? { bottom: 0 } : {}]">
			<view class="page__bottom__block" @click="handleEditText">
				<image src="../../static/images/edit.png" class="page__bottom__block__icon" mode="aspectFill" />
				<text class="page__bottom__block__text">编辑文字</text>
			</view>
			<view class="page__bottom__block" @click="handleEditFontStyle">
				<image src="../../static/images/fontstyle.png" class="page__bottom__block__icon" mode="aspectFill" />
				<text class="page__bottom__block__text">编辑样式</text>
			</view>
		</view>

		<view class="page__bottom--column" :style="[editState===4 ? { bottom: 0, height: '360rpx' }
					: {}]">
			<view class="fontstyle">
				<view class="fontstyle__tab">
					<view v-for="(tab,index) in ['大小', '颜色', '格式']" class="fontstyle__tab__item" :key="tab"
						:style="[fontStyleTab===index ? { color: '#000' } :{}]" @click="handleFontTab(index)">
						{{tab}}
					</view>
				</view>
				<view class="fontstyle__block">
					<view v-if="fontStyleTab === 0">
						<slider class="fontstyle__progress" :value="sliderValue" max="80" min="20" show-value
							@change="sliderChange" />
					</view>
					<view v-else-if="fontStyleTab === 1">
						<view class="page__bottom__color__list">
							<view v-for="color in colors" :key="color" class="page__bottom__color" :style="[{
						                        backgroundColor: color,
						                        border: selectedColor === color ? '4rpx solid #1A7AF8' : 'none',}]"
								@click="handleSelectColor(color)" />
						</view>
					</view>
					<view class="fontstyle__block" v-else-if="fontStyleTab === 2">
						<image class="fontstyle__item" src="../../static/images/delectline.png"
							:style="[textDecoration==='line-through' ? {opacity: 1 } : {}]"
							@click="handleTextStyle({ decoration: 'line-through' })" />
						<image class="fontstyle__item" src="../../static/images/underline.png"
							:style="[textDecoration==='underline' ? {opacity: 1 } : {}]"
							@click="handleTextStyle({ decoration: 'underline' })" />
						<view class="fontstyle__item__divide" />
						<image class="fontstyle__item" src="../../static/images/alignleft.png"
							:style="[textAlign==='left' ? {opacity: 1 } : {}]"
							@click="handleTextStyle({ align: 'left' })" />
						<image class="fontstyle__item" src="../../static/images/aligncenter.png"
							:style="[textAlign==='center' ? {opacity: 1 } : {}]"
							@click="handleTextStyle({ align: 'center' })" />
						<image class="fontstyle__item" src="../../static/images/alignright.png"
							:style="[textAlign==='right' ?{ opacity: 1 } : {}]"
							@click="handleTextStyle({ align: 'right'})" />
						<view class="fontstyle__item__divide" />
						<image class="fontstyle__item" src="../../static/images/bold.png"
							:style="[fontWeight==='bold' ? {opacity: 1 } : {}]"
							@click="handleTextStyle({ weight: 'bold' })" />
						<image class="fontstyle__item" src="../../static/images/italic.png"
							:style="[textStyle==='italic' ? { opacity: 1 } : {}]"
							@click="handleTextStyle({ style: 'italic' })" />
					</view>
				</view>
			</view>
			<view class="page__bottom__action">
				<image @click="handleActionCancel" src="../../static/images/close.png"
					class="page__bottom__action__icon" mode="aspectFill" />
				<text class="page__bottom__action__title">{{actionTitle}}</text>
				<image @click="handleActionConfirm" src="../../static/images/right.png"
					class="page__bottom__action__icon" mode="aspectFill" />
			</view>
		</view>
	</view>
</template>

<script>
	import {
		getTemplate
	} from "./palette/dance-example";

	const EditState = {
		NORMAL: 0,
		INPUT: 1,
		SELECT_COLOR: 2,
		TEXT: 3,
		EDIT_TEXT: 4,
	};

	export default {
		data() {
			this.currentPalette = undefined;
			this.currentView = undefined;
			this.preColor = "";
			this.preState = EditState.NORMAL;
			this.preView = undefined;
			this.history = [];
			this.future = [];
			return {
				colors: [
					'#383C42',
					'#656C75',
					'#949DA8',
					'#FFFFFF',
					'#FF2B00',
					'#C90F00',
					'#FF4081',
					'#FFA155',
					'#FFD500',
					'#FFF5B7',
					'#FFF2E5',
					'#16D9A8',
					'#00CDFF',
					'#3887FF',
				],
				customActionStyle: {
					scale: {
						textIcon: "/static/images/painter-switch.png",
						imageIcon: "/static/images/painter-scale.png",
					},
					delete: {
						icon: "/static/images/painter-close.png",
					},
				},
				inputValue: '',
				editState: EditState.NORMAL,
				selectedColor: "",
				action: [],
				clearActionBox: false,
				palette: {},
				dancePalette: {},
				hasRevert: false,
				hasRecover: false,
				actionTitle: '',
				fontStyleTab: 0,
				sliderValue: 0,
				textStyle: 'normal',
				textAlign: 'center',
				fontWeight: 'normal',
				textDecoration: 'none',
			};
		},
		watch: {
			clearActionBox: function(newValue, oldValue) {
				if (this.clearActionBox === true) {
					this.clearActionBox = false;
				}
			},
		},
		onLoad() {
			uni.showLoading({
				title: "加载中",
			});
			this.currentPalette = getTemplate();
			this.refreshPalette();
			this.preColor = this.currentPalette.background;
			this.selectedColor = this.currentPalette.background;
		},
		methods: {
			handleEditText() {
				this.preState = this.editState;
				this.editState = EditState.INPUT;
				this.actionTitle = '编辑文字';
			},
			handleChangeBackground() {
				this.preColor = this.currentPalette.background;
				this.editState = EditState.SELECT_COLOR;
				this.actionTitle = '更换背景';
			},
			getBlankTextView(text) {
				return {
					type: 'text',
					text: text || '',
					id: `text_${new Date().getTime()}${Math.ceil(Math.random() * 10)}`,
					css: {
						scalable: true,
						deletable: true,
						width: '384rpx',
						fontSize: '36rpx',
						color: '#000',
						textAlign: 'center',
						padding: '0 8rpx 8rpx 8rpx',
						top: '50%',
						left: '50%',
						align: 'center',
						verticalAlign: 'center',
					},
				};
			},
			handleActionCancel() {
				const {
					editState
				} = this;
				const newState = {
					editState: this.preState,
				};
				switch (editState) {
					case EditState.INPUT: {
						newState['inputValue'] = '';
						break;
					}
					case EditState.SELECT_COLOR: {
						if (this.currentView) {
							this.currentView.css.color = this.preColor;
							this.refreshSelectView();
						} else {
							this.currentPalette.background = this.preColor;
							this.refreshPalette();
						}
						newState['selectedColor'] = this.preColor;
					}
					case EditState.EDIT_TEXT: {
						this.currentView = this.preView;
						this.refreshSelectView();
						break;
					}
					default: {
						break;
					}
				}
				for (let key in newState) {
					this[key] = newState[key];
				}
			},

			handleActionConfirm() {
				const {
					editState,
					inputValue
				} = this;
				const newState = {
					editState: this.preState,
				};
				switch (editState) {
					case EditState.INPUT: {
						newState['inputValue'] = '';
						if (this.currentView && this.currentView.type === 'text') {
							this.pushToHistory({
								view: JSON.parse(JSON.stringify(this.currentView)),
							});
							this.currentView.text = inputValue;
							this.refreshSelectView();
						} else {
							this.pushToHistory({
								type: 'delete',
								index: this.currentPalette.views.length,
								view: this.getBlankTextView(inputValue),
							});
							this.currentPalette.views.push(this.getBlankTextView(inputValue));
							this.refreshPalette();
						}
						break;
					}
					case EditState.SELECT_COLOR: {
						this.pushToHistory({
							palette: JSON.parse(
								JSON.stringify({
									...this.currentPalette,
									background: this.preColor,
								}),
							),
						});
						break;
					}
					case EditState.EDIT_TEXT: {
						this.pushToHistory({
							view: this.preView,
						});
						break;
					}
					default: {
						break;
					}
				}
				for (let key in newState) {
					this[key] = newState[key];
				}
			},
			handleSelectColor(color) {
				this.selectedColor = color;

				if (this.currentView && this.currentView.type === 'text') {
					this.currentView.css.color = color;
					this.refreshSelectView();
				} else {
					this.currentPalette.background = color;
					this.refreshPalette();
				}
			},
			sliderChange({
				detail
			}) {
				if (this.currentView.css.lineHeight) {
					this.currentView.css.lineHeight = parseInt(this.currentView.css.lineHeight) -
						parseInt(this.currentView.css.fontSize) +
						detail.value;
					this.currentView.css.lineHeight += "rpx";
				}
				this.currentView.css.fontSize = `${detail.value}rpx`;
				this.refreshSelectView();
			},
			handleInput({
				detail
			}) {
				this.inputValue = detail.value;
			},
			handleFontTab(index) {
				this.fontStyleTab = index;
			},
			handleEditFontStyle() {
				this.preView = JSON.parse(JSON.stringify(this.currentView));
				this.preState = this.editState;
				this.editState = EditState.EDIT_TEXT;
				this.actionTitle = '编辑样式';
				this.selectedColor = this.currentView.css.color;
				this.sliderValue = parseInt(this.currentView.css.fontSize);
				this.textStyle = this.currentView.css.textStyle || 'normal';
				this.textAlign = this.currentView.css.textAlign || 'center';
				this.fontWeight = this.currentView.css.fontWeight || 'normal';
				this.textDecoration = this.currentView.css.textDecoration || 'none';
			},
			// align?: string; weight?: string; style?: string; decoration?: string 
			handleTextStyle(options) {
				const {
					align,
					weight,
					style,
					decoration
				} = options;
				const newState = {};
				if (align) {
					if (this.currentView.css.textAlign === align) {
						delete this.currentView.css.textAlign;
						newState['textAlign'] = '';
					} else {
						this.currentView.css.textAlign = align;
						newState['textAlign'] = align;
					}
				}
				if (weight) {
					if (this.currentView.css.fontWeight === weight) {
						this.currentView.css.fontWeight = 'normal';
						newState['fontWeight'] = 'normal';
					} else {
						this.currentView.css.fontWeight = weight;
						newState['fontWeight'] = weight;
					}
				}
				if (style) {
					if (this.currentView.css.textStyle === style) {
						this.currentView.css.textStyle = 'normal';
						newState['textStyle'] = 'normal';
					} else {
						this.currentView.css.textStyle = style;
						newState['textStyle'] = style;
					}
				}
				if (decoration) {
					if (this.currentView.css.textDecoration === decoration) {
						this.currentView.css.textDecoration = 'none';
						newState['textDecoration'] = 'none';
					} else {
						this.currentView.css.textDecoration = decoration;
						newState['textDecoration'] = decoration;
					}
				}
				for (let key in newState) {
					this[key] = newState[key];
				}
				this.refreshSelectView();
			},

			refreshPalette(palette) {
				this.dancePalette = palette || {
					...this.currentPalette,
				};
			},
			handleImgOk(e) {
				uni.hideLoading();
				uni.saveImageToPhotosAlbum({
						filePath: e.detail.path,
					})
					.then(() => {
						uni.showToast({
							icon: "none",
							title: "保存成功",
						});
					})
					.catch((err) => {
						console.error(err);
						Taro.showToast({
							icon: "none",
							title: "保存失败",
						});
					});
			},
			handleDidShow() {
				uni.hideLoading();
			},
			handleTouchEnd({
				detail
			}) {
				if (detail.type === "delete") {
					this.pushToHistory(JSON.parse(JSON.stringify(detail)));
					const _views = this.currentPalette.views;
					if (this.currentPalette.views[detail.index].id === detail.view.id) {
						this.currentPalette.views.splice(detail.index, 1);
					} else {
						this.currentPalette.views.splice(detail.index, 0, detail.view);
					}
					this.refreshPalette();
					this.handleClickBackground();
				} else if (!detail.view) {
					this.handleClickBackground();
				} else {
					const _views = JSON.parse(JSON.stringify(this.currentPalette.views));
					for (let i = 0; i < _views.length; i++) {
						if (_views[i].id === detail.view.id) {
							this.pushToHistory({
								view: JSON.parse(JSON.stringify(_views[i])),
							});
							_views[i] = {
								...detail.view,
							};
							this.currentPalette.views = _views;
							break;
						}
					}
				}
			},
			handleViewClick(e) {
				const {
					view
				} = e.detail;
				this.currentView = view;
				if (view && view.type && view.type === "text") {
					this.editState = EditState.TEXT;
				}
			},
			handleViewUpdate(view) {},
			handleClickBackground() {
				this.currentView = undefined;
				this.editState = EditState.NORMAL;
				this.clearActionBox = true;
			},

			pushToHistory(item) {
				this.future.length = 0;
				while (this.history.length > 19) {
					this.history.shift();
				}
				this.history.push(item);
				this.refreshTop();
			},
			refreshTop() {
				this.hasRecover = this.future.length > 0;
				this.hasRevert = this.history.length > 0;
			},
			handleSaveImage() {
				uni.showLoading({
					title: "生成中",
				});
				this.palette = JSON.parse(JSON.stringify(this.currentPalette));
			},
			refreshSelectView(view) {
				if (view || this.currentView) {
					this.action = {
						view: view || this.currentView,
					};
				}
			},
			handleTimeMachine(type) {
				let popStack = [];
				let pushStack = [];
				if (type === "revert") {
					popStack = this.history;
					pushStack = this.future;
				} else {
					pushStack = this.history;
					popStack = this.future;
				}
				const pre = popStack.pop();
				if (!pre) {
					return;
				}
				if (pre.type === "delete") {
					this.currentView = undefined;
					if (
						this.currentPalette.views[pre.index] &&
						this.currentPalette.views[pre.index].id === pre.view.id
					) {
						this.currentPalette.views.splice(pre.index, 1);
					} else {
						this.currentPalette.views.splice(pre.index, 0, pre.view);
					}
					pushStack.push(pre);
					this.refreshPalette();
				} else if (pre.palette) {
					pushStack.push({
						palette: JSON.parse(JSON.stringify(this.currentPalette)),
					});
					this.currentPalette = pre.palette;
					this.currentView = undefined;
					this.refreshPalette();
				} else {
					for (let i = 0; i < this.currentPalette.views.length; i++) {
						if (this.currentPalette.views[i].id === pre.view.id) {
							pushStack.push({
								view: JSON.parse(JSON.stringify(this.currentPalette.views[i])),
							});
							this.currentPalette.views[i] = pre.view;
							this.currentView = this.currentPalette.views[i];
							this.refreshSelectView(pre.view);
							break;
						}
					}
				}
				this.editState = this.currentView && this.currentView.type === "text" ? EditState.TEXT : EditState.NORMAL;
				this.refreshTop();
			},
		},
	};
</script>

<style lang="scss">
	@mixin bottom {
		position: absolute;
		display: flex;
		bottom: -100%;
		left: 0;
		width: 100vw;
		height: 240rpx;
		background-color: #fff;
		transition-duration: 0.5s;
	}

	.page {
		&__bg {
			height: 100vh;
			width: 100vw;
			display: flex;
			flex-direction: column;
			align-items: center;
			position: relative;
			background-color: #f5f7fa;
		}

		&__bottom {
			@include bottom();

			&--column {
				@include bottom();
				flex-direction: column;
			}

			&__block {
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;
				flex-grow: 1;

				&__icon {
					height: 44rpx;
					width: 44rpx;
				}

				&__text {
					font-size: 22rpx;
					color: #383c42;
					text-align: center;
					margin-top: 19rpx;
				}
			}

			&__input {
				background-color: #eee;
				margin: 30rpx;
				padding: 10rpx 20rpx;
				border-radius: 4rpx;
			}

			&__action {
				width: 100vw;
				display: flex;
				justify-content: space-between;
				align-items: center;

				&__icon {
					height: 36rpx;
					width: 36rpx;
					padding: 20rpx 40rpx;
				}

				&__title {
					font-size: 26rpx;
					text-align: center;
				}
			}

			&__color {
				height: 60rpx;
				border-radius: 4rpx;
				margin: 16rpx;
				min-width: 60rpx;
				box-sizing: border-box;

				&__list {
					display: flex;
					flex-wrap: nowrap;
					align-items: center;
					overflow-y: hidden;
					overflow-x: scroll;
					width: calc(100vw - 40rpx);
					margin: 20rpx;

					&::-webkit-scrollbar {
						display: none;
					}
				}
			}
		}
	}

	.bg {
		position: absolute;
		left: 0;
		top: 0;
		height: 100%;
		width: 100%;
	}

	.fontstyle {
		&__tab {
			display: flex;
			width: 100vw;
			justify-content: center;

			&__item {
				font-size: 28rpx;
				width: 130rpx;
				text-align: center;
				padding: 20rpx 0;
				color: #999;
			}
		}

		&__block {
			display: flex;
			height: 160rpx;
			align-items: center;
			justify-content: center;
		}

		&__progress {
			width: 80vw;
		}

		&__item {
			height: 44rpx;
			width: 44rpx;
			padding: 20rpx;
			opacity: 0.3;

			&__divide {
				height: 36rpx;
				width: 4rpx;
				margin: 20rpx;
				background-color: #383c42;
			}
		}
	}

	.top {
		align-self: flex-start;
		margin-top: 1vh;
		padding-left: 40rpx;
		z-index: 10;
		position: relative;
		width: 100vw;
		box-sizing: border-box;

		&__block {
			display: inline-flex;
			padding: 20rpx 20rpx;
			align-items: center;
			justify-content: center;
			font-size: 28rpx;
			color: #383c42;

			&__icon {
				height: 30rpx;
				width: 30rpx;
				margin-right: 20rpx;
			}
		}

		&__button {
			display: inline-flex;
			position: absolute;
			right: 40rpx;
			top: 10rpx;
			padding: 4rpx 20rpx;
			border-radius: 8rpx;
			background-color: #2b7be3;
			font-size: 28rpx;
			color: #fff;
		}
	}
</style>
