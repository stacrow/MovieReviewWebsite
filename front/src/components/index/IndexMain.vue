<template>
	<el-main>
		<bread-crumbs :title="title" class="bread-crumbs"></bread-crumbs>
		<router-view class="router-view"></router-view>
	</el-main>
</template>
<script>
	import menu from "@/utils/menu";
	export default {
		data() {
			return {
				menuList: [],
				role: "",
				currentIndex: -2,
				itemMenu: [],
				title: ''
			};
		},
		mounted() {
			let menus = menu.list();
			this.menuList = menus;
			this.role = this.$storage.get("role");
		},
		methods: {
			menuHandler(menu) {
				this.$router.push({
					name: menu.tableName
				});
				this.title = menu.menu;
			},
			titleChange(index, menus) {
				this.currentIndex = index
				this.itemMenu = menus;
				console.log(menus);
			},
			homeChange(index) {
				this.itemMenu = [];
				this.title = ""
				this.currentIndex = index
				this.$router.push({
					name: 'home'
				});
			},
			centerChange(index) {
				this.itemMenu = [{
					"buttons": ["新增", "查看", "修改", "删除"],
					"menu": "修改密码",
					"tableName": "updatePassword"
				}, {
					"buttons": ["新增", "查看", "修改", "删除"],
					"menu": "个人信息",
					"tableName": "center"
				}];
				this.title = ""
				this.currentIndex = index
				this.$router.push({
					name: 'home'
				});
			}
		}
	};
</script>
<style lang="scss" scoped>
	a {
		text-decoration: none;
		color: #34017b;
	}

	a:hover {
		background: rgba(52, 1, 123, 0.1);
	}

	.nav-list {
		width: 100%;
		margin: 0 auto;
		text-align: left;
		margin-top: 20px;

		.nav-title {
			display: inline-block;
			font-size: 15px;
			color: #34017b;
			padding: 15px 25px;
			border: none;
		}

		.nav-title.active {
			color: #34017b;
			cursor: default;
			background-color: rgba(52, 1, 123, 0.1);
		}
	}

	.nav-item {
		margin-top: 20px;
		background: #FFFFFF;
		padding: 15px 0;
		border-radius: 8px;
		box-shadow: 0 2px 12px rgba(52, 1, 123, 0.1);

		.menu {
			padding: 15px 25px;
			color: #34017b;
		}
	}

	.el-main {
		background-color: #f8f5ff;
		padding: 0 24px;
	}

	.router-view {
		padding: 20px;
		margin-top: 15px;
		background: #FFFFFF;
		box-sizing: border-box;
		border-radius: 8px;
		box-shadow: 0 2px 12px rgba(52, 1, 123, 0.1);
	}

	.bread-crumbs {
		width: 100%;
		margin-top: 15px;
		box-sizing: border-box;
		padding: 10px 0;
	}
</style>
