<template>
	<!-- <el-header>
		<el-menu background-color="#00c292" text-color="#FFFFFF" active-text-color="#FFFFFF" mode="horizontal">
			<div class="fl title">{{this.$project.projectName}}</div>
			<div class="fr logout" style="display:flex;">
				<el-menu-item index="3">
					<div>{{this.$storage.get('role')}} {{this.$storage.get('adminName')}}</div>
				</el-menu-item>
				<el-menu-item @click="onLogout" index="2">
					<div>退出登录</div>
				</el-menu-item>
			</div>
		</el-menu>
	</el-header> -->
	<div class="navbar" :style="{backgroundColor:heads.headBgColor,height:heads.headHeight,boxShadow:heads.headBoxShadow,lineHeight:heads.headHeight}">
		<div class="title-menu" :style="{justifyContent:heads.headTitleStyle=='1'?'flex-start':'center'}">
			<el-image v-if="heads.headTitleImg" class="title-img" :style="{width:heads.headTitleImgWidth,height:heads.headTitleImgHeight,boxShadow:heads.headTitleImgBoxShadow,borderRadius:heads.headTitleImgBorderRadius}" :src="heads.headTitleImgUrl" fit="cover"></el-image>
			<div class="title-name">{{this.$project.projectName}}</div>
		</div>
		<div class="right-menu">
			<div class="user-info" :style="{color:heads.headUserInfoFontColor,fontSize:heads.headUserInfoFontSize}">
				<i class="el-icon-user"></i>
				{{this.$storage.get('role')}} {{this.$storage.get('adminName')}}
			</div>
			<div class="logout" :style="{color:heads.headLogoutFontColor,fontSize:heads.headLogoutFontSize}" @click="onIndexTap">
				<i class="el-icon-s-home"></i>
				返回前台
			</div>
			<div class="logout" :style="{color:heads.headLogoutFontColor,fontSize:heads.headLogoutFontSize}" @click="onLogout">
				<i class="el-icon-switch-button"></i>
				退出登录
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				dialogVisible: false,
				ruleForm: {},
				user: {},
				heads: {"headLogoutFontHoverColor":"#fff","headFontSize":"20px","headUserInfoFontColor":"rgba(0, 0, 0, 1)","headBoxShadow":"0px 8px 0px #34017b","headTitleImgHeight":"44px","headLogoutFontHoverBgColor":"rgba(0, 0, 0, 1)","headFontColor":"rgba(255, 255, 255, 1)","headTitleImg":false,"headHeight":"50px","headTitleImgBorderRadius":"22px","headTitleImgUrl":"http://codegen.caihongy.cn/20201021/cc7d45d9c8164b58b18351764eba9be1.jpg","headBgColor":"#EEEEEE","headTitleImgBoxShadow":"0 1px 6px #444","headLogoutFontColor":"rgba(0, 0, 0, 1)","headUserInfoFontSize":"16px","headTitleImgWidth":"44px","headTitleStyle":"2","headLogoutFontSize":"16px"},
			};
		},
		created() {
			this.setHeaderStyle()
		},
		mounted() {
			let sessionTable = this.$storage.get("sessionTable")
			this.$http({
				url: sessionTable + '/session',
				method: "get"
			}).then(({
				data
			}) => {
				if (data && data.code === 0) {
					this.user = data.data;
				} else {
					let message = this.$message
					message.error(data.msg);
				}
			});
		},
		methods: {
			onLogout() {
				let storage = this.$storage
				let router = this.$router
				storage.clear()
				router.replace({
					name: "login"
				});
			},
      			onIndexTap(){
      				window.location.href = `${this.$base.indexUrl}`
    			},
			setHeaderStyle() {
			  this.$nextTick(()=>{
			    document.querySelectorAll('.navbar .right-menu .logout').forEach(el=>{
			      el.addEventListener("mouseenter", e => {
			        e.stopPropagation()
			        el.style.backgroundColor = this.heads.headLogoutFontHoverBgColor
					el.style.color = this.heads.headLogoutFontHoverColor
			      })
			      el.addEventListener("mouseleave", e => {
			        e.stopPropagation()
			        el.style.backgroundColor = "transparent"
					el.style.color = this.heads.headLogoutFontColor
			      })
			    })
			  })
			},
		}
	};
</script>


<style lang="scss" scoped>
	.navbar {
		height: 50px !important;
		line-height: 50px !important;
		width: 100%;
		padding: 0 24px;
		box-sizing: border-box;
		background: #34017b;
		position: relative;
		z-index: 111;
		box-shadow: 0 2px 12px rgba(52, 1, 123, 0.2);
		display: flex;
		justify-content: space-between;
		align-items: center;
		
		.right-menu {
			display: flex;
			justify-content: flex-end;
			align-items: center;
			z-index: 111;
			
			.user-info {
				font-size: 14px;
				color: #fff;
				padding: 0 12px;
				display: flex;
				align-items: center;
				height: 32px;
				line-height: 32px;
				
				i {
					margin-right: 6px;
					font-size: 16px;
				}
			}
			
			.logout {
				font-size: 14px;
				color: #fff;
				padding: 0 12px;
				cursor: pointer;
				transition: all 0.3s;
				border-radius: 4px;
				display: flex;
				align-items: center;
				height: 32px;
				line-height: 32px;
				
				i {
					margin-right: 6px;
					font-size: 16px;
				}
				
				&:hover {
					background: rgba(255, 255, 255, 0.1);
				}
			}
		}
		
		.title-menu {
			display: flex;
			justify-content: flex-start;
			align-items: center;
			height: 100%;
			
			.title-img {
				width: 32px;
				height: 32px;
				border-radius: 16px;
				box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
				margin-right: 12px;
			}
			
			.title-name {
				font-size: 18px;
				color: #000000;
				font-weight: 600;
				text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
			}
		}
	}

	:deep(.el-header) {
		height: 50px !important;
		line-height: 50px !important;
		padding: 0 !important;
	}

	// .el-header .fr {
	// 	float: right;
	// }

	// .el-header .fl {
	// 	float: left;
	// }

	// .el-header {
	// 	width: 100%;
	// 	color: #333;
	// 	text-align: center;
	// 	line-height: 60px;
	// 	padding: 0;
	// 	z-index: 99;
	// }

	// .logo {
	// 	width: 60px;
	// 	height: 60px;
	// 	margin-left: 70px;
	// }

	// .avator {
	// 	width: 40px;
	// 	height: 40px;
	// 	background: #ffffff;
	// 	border-radius: 50%;
	// }

	// .title {
	// 	color: #ffffff;
	// 	font-size: 20px;
	// 	font-weight: bold;
	// 	margin-left: 20px;
	// }
</style>
