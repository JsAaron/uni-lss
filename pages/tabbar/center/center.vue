<template>
	<view>
		<!-- 顶部 -->
		<view class="banner">
			<view class="banner__left">
				<image class="banner__logo" :src="avatarUrl" />
				<view>
					<view class="banner__t">
						<text>{{ nickName }}</text>
						<text v-if="isfact" class="banner__shimimg">已认证</text>
						<text v-else class="banner__shimimg" @tap="gotoPage" data-url="./certification">
							{{ hasLogin ? '未实名' : '未登录' }}
						</text>
					</view>
					<view class="banner__t banner__t2">
						<text>{{ mobileno }}</text>
					</view>
				</view>
			</view>
			<view class="banner__right" bindtap="bindGotoMerchant">
				<image class="banner__m" :src="getLevel" />
				<view>金牌创客</view>
			</view>
		</view>

		<!-- 统计 -->
		<view class="statistics">
			<view class="statistics__row">
				<view class="statistics__col">
					<text class="statistics__m">100元</text>
					<text class="statistics__t">可提现</text>
				</view>
				<view class="statistics__col">
					<text class="statistics__m">100元</text>
					<text class="statistics__t">总收入</text>
				</view>
				<view class="statistics__arrow">
					<text class="statistics__t">收支明细</text>
					<uni-icon type="arrowright" class="statistics__arrow-icon" size="16"></uni-icon>
				</view>
			</view>
			<view class="statistics__button">
				<waves-button
					btnStyle="background:linear-gradient(to right,#FF9A29, #FF6B0E);height:65rpx;border-radius:10px"
					wavesColor="rgba(255,101,16,.6)"
					txt="提现"
				></waves-button>
			</view>
		</view>

		<!-- 订单 -->
		<view class="order">
			<view class="order__tab lss-hairline--bottom">
				<text>我的订单</text>
				<view class="order__all">
					<text>查看全部订单</text>
					<uni-icon type="arrowright" size="16"></uni-icon>
				</view>
			</view>
			<view class="order__nav">
				<view class="order__col">
					<image src="/static/tabbar/center/5.png"></image>
					<text>全部</text>
				</view>
				<view class="order__col">
					<image src="/static/tabbar/center/6.png"></image>
					<text>待付款</text>
				</view>
				<view class="order__col">
					<image src="/static/tabbar/center/7.png"></image>
					<text>待使用</text>
				</view>
				<view class="order__col">
					<image src="/static/tabbar/center/8.png"></image>
					<text>待评价</text>
				</view>
			</view>
		</view>

		<!-- 必备工具 -->
		<view class="tools">
			<view class="tools__tab lss-hairline--bottom"><text>必备工具</text></view>
			<view class="tools__nav">
				<view class="tools__col" @tap="gotoPage" data-url="./bank-card">
					<image src="/static/tabbar/center/t1.png"></image>
					<text>银行卡</text>
				</view>
				<view class="tools__col" @tap="gotoPage" data-url="./team">
					<image src="/static/tabbar/center/t2.png"></image>
					<text>我的团队</text>
				</view>
				<view class="tools__col">
					<image src="/static/tabbar/center/t3.png"></image>
					<text>分享推广</text>
				</view>
				<view class="tools__col" @tap="gotoPage" data-url="/pages/common/settings">
					<image src="/static/tabbar/center/t4.png"></image>
					<text>个人设置</text>
				</view>
				<view class="tools__col">
					<image src="/static/tabbar/center/t5.png"></image>
					<text>常见问答</text>
				</view>
				<view class="tools__col">
					<image src="/static/tabbar/center/t6.png"></image>
					<text>结算规则</text>
				</view>
				<view class="tools__col">
					<image src="/static/tabbar/center/t7.png"></image>
					<text>联系客服</text>
				</view>
				<view class="tools__col">
					<image src="/static/tabbar/center/t8.png"></image>
					<text>申请商户</text>
				</view>
				<view class="tools__col">
					<image src="/static/tabbar/center/t9.png"></image>
					<text>我的奖券</text>
				</view>
				<view class="tools__col">
					<image src="/static/tabbar/center/t10.png"></image>
					<text>我的收藏</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import * as util from '@/utils';
import { $$set, $$get } from '@/common/global';
import uniIcon from '@/components/uni/uni-icons/uni-icons';
import wavesButton from '@/components/waves-button/waves-button';
export default {
	components: {
		uniIcon,
		wavesButton
	},
	data() {
		return {
			avatarUrl: '',
			nickName: '',
			hasLogin: '',
			user_level_name: '',
			mobileno: ''
		};
	},
	onShow() {
		this.mobileno = util.phoneEncrypt($$get.login('mobileno'));
		this.hasLogin = this.$api.hasLogin();
		this.user_level_name = $$get.login('user_level_name');
		this.isfact = $$get.login('isfact');
		this.nickName = $$get.userInfo('nickName');
		this.avatarUrl = $$get.userInfo('avatarUrl');
		// this.getInitData();
	},
	props: {},
	created() {},
	onLoad() {},
	computed: {
		getLevel() {
			let level = 2 || $$get.login('user_level');
			return `/static/tabbar/center/${level}.png`;
		}
	},
	methods: {
		/**
		 * 跳页
		 */
		gotoPage(e) {
			if (!this.$api.hasLogin({ hint: true })) {
				return;
			}
			let url = e.currentTarget.dataset.url;

			//必须认证实名
			if (!$$get.login('isfact')) {
				let certUrl = './certification';

				//跳过认证 和 设置
				if (url == certUrl || url == '/pages/common/settings') {
					return util.gotoPage(url);
				}

				wx.showModal({
					content: `亲,您还没有实名认证哦！`,
					confirmText: '去认证',
					success(res) {
						if (res.confirm) {
							util.gotoPage(certUrl);
						}
					}
				});
				return;
			}

			//已经认证
			if (url == '/pages/user/pages/my/certification/certification') {
				util.showToast('您已实名认证过啦！');
				return;
			}

			//已经加入创客
			if (
				url == '/pages/user/pages/my/partner/partner' &&
				app.globalData.login.user_level &&
				app.globalData.login.user_level != 0
			) {
				util.showToast('您已加入创客啦！');
				return;
			}

			//已经开通服务商
			if (url == '/pages/subpackage/service-provider/apply-for/apply-for' && app.globalData.login.has_fws) {
				util.showToast('您已是服务商啦！');
				return;
			}

			//进页面
			return util.gotoPage(url);
		}
	}
};
</script>

<style lang="scss">
.banner {
	@include flex-h-between(flex-start);
	width: 100vw;
	z-index: 1;
	height: 200rpx;
	background-image: url('~@/static/tabbar/center/banner.png');
	background-size: 100% 100%;
	color: $text-color-white;
	&__left {
		height: 150rpx;
		@include flex-h-left;
	}

	&__logo {
		width: 100rpx;
		height: 100rpx;
		border-radius: 50%;
		background: #ffffff;
		margin: 0 30rpx;
	}

	&__vip {
		width: 90rpx;
		height: 25rpx;
	}

	&__vip-text {
		font-size: 25rpx;
		color: $text-color-gray;
	}

	&__m {
		width: 48rpx;
		height: 48rpx;
	}

	&__right {
		@include flex-v;
		margin-right: 30rpx;
		text-align: center;
		font-size: 26rpx;
		height: 150rpx;
	}

	&__t {
		@include flex-h-left;
	}

	&__t2 {
		font-size: 30rpx;
		margin-top: 20rpx;
	}

	&__shimimg {
		margin-left: 10rpx;
		font-weight: bold;
		font-size: 22rpx;
		border: 1px solid $gray;
		background: #fff;
		border-radius: 30rpx;
		color: $text-color-gray;
		padding: 5rpx 15rpx;

		&:active {
			background: $gray;
		}
	}
}

//统计
.statistics {
	width: 690rpx;
	position: absolute;
	padding: 30rpx 0;
	left: 30rpx;
	z-index: 1;
	top: 140rpx;
	background: $background-color-white;
	border-radius: 10rpx;
	&__row {
		@include flex-h-around;
	}
	&__col {
		@include flex-v;
	}
	&__arrow {
		@include flex-h;
	}
	&__arrow-icon {
		height: 30rpx;
	}
	&__button {
		width: 480rpx;
		margin: 0 auto;
		padding-top: 30rpx;
	}
	&__m {
		color: $text-color-orange;
		font-weight: bold;
	}
	&__t {
		font-size: 26rpx;
	}
}

//订单
.order {
	width: 690rpx;
	margin: 200rpx auto 0;
	background: $background-color-white;
	border-radius: 10rpx;
	&__tab {
		@include flex-h-between;
		padding: 20rpx 30rpx;
		font-size: 14px;
	}
	&__all {
		@include flex-h;
		font-size: 12px;
		color: $text-color-gray;
	}
	&__nav {
		padding: 30rpx 0;
		@include flex-h-around;
	}
	&__col {
		@include flex-v;
		font-size: 12px;
		image {
			width: 42rpx;
			height: 42rpx;
		}
		text {
			margin-top: 10rpx;
		}
	}
}

//必备工具
.tools {
	width: 690rpx;
	margin: 20rpx auto 0;
	background: $background-color-white;
	border-radius: 10rpx;
	&__tab {
		@include flex-h-between;
		padding: 20rpx 30rpx;
		font-size: 14px;
	}
	&__nav {
		display: flex;
		flex-wrap: wrap;
		padding: 10rpx 0 30rpx 0;
	}
	&__col {
		@include flex-v;
		width: 25%;
		margin-top: 50rpx;
		image {
			width: 64rpx;
			height: 64rpx;
		}
		text {
			margin-top: 10rpx;
			font-size: 12px;
		}
		&:active {
			color: $text-color-blue;
		}
	}
}
</style>
