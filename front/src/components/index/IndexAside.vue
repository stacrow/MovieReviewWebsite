<template>
  <el-aside class="index-aside" width="240px">
    <div class="index-aside-inner">
      <el-menu 
        default-active="1"
        :collapse="isCollapse"
        class="el-menu-vertical"
        @select="handleSelect">
        <el-menu-item index="1" @click="menuHandler('/')">
          <i class="el-icon-s-home"></i>
          <span slot="title">首页</span>
        </el-menu-item>
        <sub-menu
          v-for="menu in menuList"
          :key="menu.menuId"
          :menu="menu"
          :dynamicMenuRoutes="dynamicMenuRoutes"
        ></sub-menu>
      </el-menu>
    </div>
  </el-aside>
</template>

<script>
import SubMenu from "@/components/index/IndexAsideSub";

export default {
  components: {
    SubMenu
  },
  data() {
    return {
      menuList: [],
      dynamicMenuRoutes: [],
      isCollapse: false
    };
  },
  mounted() {
    this.menuList = JSON.parse(sessionStorage.getItem("menuList") || "[]");
    this.dynamicMenuRoutes = JSON.parse(
      sessionStorage.getItem("dynamicMenuRoutes") || "[]"
    );
  },
  methods: {
    menuHandler(path) {
      this.$router.push({ path: path });
    },
    handleSelect(key, keyPath) {
      console.log(key, keyPath);
    }
  }
};
</script>

<style lang="scss" scoped>
.index-aside {
  margin-top: 50px;
  height: calc(100vh - 50px);
  overflow: hidden;
  background: #fff;
  border-right: 1px solid #e5e7eb;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 999;
  transition: all 0.3s ease;
  
  .index-aside-inner {
    width: 240px;
    height: 100%;
    overflow-y: auto;
    padding: 12px 0;

    &::-webkit-scrollbar {
      width: 4px;
    }

    &::-webkit-scrollbar-thumb {
      background: #e5e7eb;
      border-radius: 2px;
    }

    .el-menu {
      border-right: none;
      background-color: transparent;

      .el-menu-item {
        height: 40px;
        line-height: 40px;
        margin: 2px 8px;
        border-radius: 6px;
        color: #4b5563;
        font-size: 14px;
        display: flex;
        align-items: center;
        
        i {
          margin-right: 8px;
          font-size: 16px;
        }
        
        &:hover {
          background-color: #f3f4f6;
          color: #1f2937;
        }

        &.is-active {
          background-color: #f3f4f6;
          color: #34017b;
          font-weight: 500;
        }
      }
    }
  }
}

:deep(.el-sub-menu__title) {
  height: 40px;
  line-height: 40px;
  margin: 2px 8px;
  border-radius: 6px;
  color: #4b5563;
  font-size: 14px;
  
  &:hover {
    background-color: #f3f4f6;
    color: #1f2937;
  }
  
  i {
    margin-right: 8px;
    font-size: 16px;
  }
}

:deep(.el-menu--collapse) {
  width: 64px;
}
</style>

