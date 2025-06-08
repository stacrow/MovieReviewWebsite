<template>
<div class="content">
  <div class="welcome-section">
    <div class="text main-text">欢迎使用 {{this.$project.projectName}}</div>
    <div class="sub-text">今天是 {{currentDate}}，祝您工作愉快！</div>
  </div>

  <div class="dashboard">
    <!-- 统计卡片区域 -->
    <el-row :gutter="20">
      <!-- 管理员统计卡片 -->
      <template v-if="isAdmin">
        <el-col :span="6">
          <el-card shadow="hover" class="stat-card">
            <div class="stat-icon"><i class="el-icon-user"></i></div>
            <div class="stat-info">
              <div class="stat-title">用户总数</div>
              <div class="stat-value">{{userCount || 0}}</div>
            </div>
          </el-card>
        </el-col>
        <el-col :span="6">
          <el-card shadow="hover" class="stat-card">
            <div class="stat-icon"><i class="el-icon-film"></i></div>
            <div class="stat-info">
              <div class="stat-title">电影总数</div>
              <div class="stat-value">{{movieCount || 0}}</div>
            </div>
          </el-card>
        </el-col>
        <el-col :span="6">
          <el-card shadow="hover" class="stat-card">
            <div class="stat-icon"><i class="el-icon-star-on"></i></div>
            <div class="stat-info">
              <div class="stat-title">留言总数</div>
              <div class="stat-value">{{messageCount || 0}}</div>
            </div>
          </el-card>
        </el-col>
        <el-col :span="6">
          <el-card shadow="hover" class="stat-card">
            <div class="stat-icon"><i class="el-icon-chat-dot-round"></i></div>
            <div class="stat-info">
              <div class="stat-title">帖子总数</div>
              <div class="stat-value">{{postCount || 0}}</div>
            </div>
          </el-card>
        </el-col>
      </template>
      <!-- 普通用户统计卡片 -->
      <template v-else>
        <el-col :span="24">
          <el-card shadow="hover" class="stat-card">
            <div class="stat-icon"><i class="el-icon-star-on"></i></div>
            <div class="stat-info">
              <div class="stat-title">我的收藏总数</div>
              <div class="stat-value">{{storeupCount || 0}}</div>
            </div>
          </el-card>
        </el-col>
      </template>
    </el-row>

    <!-- 快捷入口区域 -->
    <el-row :gutter="20" class="quick-access">
      <el-col :span="24">
        <el-card shadow="hover">
          <div slot="header">
            <span>快捷入口</span>
          </div>
          <div class="quick-links">
            <!-- 管理员快捷入口 -->
            <template v-if="isAdmin">
              <el-button type="primary" icon="el-icon-plus" @click="goToAdd('movie')">添加电影</el-button>
              <el-button type="success" icon="el-icon-user" @click="goToAdd('user')">用户管理</el-button>
              <el-button type="warning" icon="el-icon-message" @click="goToAdd('messages')">留言板管理</el-button>
              <el-button type="info" icon="el-icon-chat-line-round" @click="goToAdd('forum')">论坛管理</el-button>
            </template>
            <!-- 普通用户快捷入口 -->
            <template v-else>
              <el-button type="primary" icon="el-icon-star-on" @click="goToAdd('storeup')">收藏管理</el-button>
            </template>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</div>
</template>

<script>
import router from '@/router/router-static'
export default {
  data() {
    return {
      currentDate: '',
      userCount: 0,
      movieCount: 0,
      messageCount: 0,
      postCount: 0,
      storeupCount: 0,
      isAdmin: false
    }
  },
  mounted(){
    this.init();
    this.getCurrentDate();
    this.checkUserRole();
    this.getStatistics();
  },
  methods:{
    checkUserRole() {
      const role = this.$storage.get('role');
      this.isAdmin = role === '管理员';
    },
    init(){
      if(this.$storage.get('Token')){
        this.$http({
          url: `${this.$storage.get('sessionTable')}/session`,
          method: "get"
        }).then(({ data }) => {
          if (data && data.code != 0) {
            router.push({ name: 'login' })
          }
        });
      }else{
        router.push({ name: 'login' })
      }
    },
    getCurrentDate() {
      const now = new Date();
      this.currentDate = now.toLocaleDateString('zh-CN', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        weekday: 'long'
      });
    },
    getStatistics() {
      if (this.isAdmin) {
        // 管理员获取所有统计数据
        this.$http({
          url: 'yonghu/page',
          method: 'get',
          params: { 
            page: 1, 
            limit: 1,
            sort: 'id'
          }
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.userCount = data.data.total;
          } else {
            this.userCount = 0;
          }
        }).catch(() => {
          this.userCount = 0;
        });

        this.$http({
          url: 'dianyingxinxi/page',
          method: 'get',
          params: { 
            page: 1, 
            limit: 1,
            sort: 'id'
          }
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.movieCount = data.data.total;
          } else {
            this.movieCount = 0;
          }
        }).catch(() => {
          this.movieCount = 0;
        });

        this.$http({
          url: 'messages/page',
          method: 'get',
          params: { 
            page: 1, 
            limit: 1,
            sort: 'id'
          }
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.messageCount = data.data.total;
          } else {
            this.messageCount = 0;
          }
        }).catch(() => {
          this.messageCount = 0;
        });

        this.$http({
          url: 'forum/page',
          method: 'get',
          params: { 
            page: 1, 
            limit: 1,
            sort: 'id'
          }
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.postCount = data.data.total;
          } else {
            this.postCount = 0;
          }
        }).catch(() => {
          this.postCount = 0;
        });
      } else {
        // 普通用户只获取收藏总数
        this.$http({
          url: 'storeup/page',
          method: 'get',
          params: { 
            page: 1, 
            limit: 1,
            sort: 'id',
            userid: this.$storage.get('userId')
          }
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.storeupCount = data.data.total;
          } else {
            this.storeupCount = 0;
          }
        }).catch(() => {
          this.storeupCount = 0;
        });
      }
    },
    goToAdd(type) {
      switch(type) {
        case 'movie':
          router.push('/dianyingxinxi');
          break;
        case 'user':
          if (this.isAdmin) {
            router.push('/yonghu');
          }
          break;
        case 'messages':
          router.push('/messages');
          break;
        case 'forum':
          router.push('/forum');
          break;
        case 'storeup':
          router.push('/storeup');
          break;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.content {
  padding: 20px;
  .welcome-section {
    text-align: center;
    margin-bottom: 40px;
    .main-text {
      font-size: 38px;
      font-weight: bold;
      color: #34017b;
      margin-bottom: 10px;
    }
    .sub-text {
      font-size: 16px;
      color: #666;
    }
  }
  .dashboard {
    .stat-card {
      height: 120px;
      display: flex;
      align-items: center;
      padding: 20px;
      .stat-icon {
        font-size: 48px;
        color: #34017b;
        margin-right: 20px;
      }
      .stat-info {
        .stat-title {
          font-size: 14px;
          color: #666;
          margin-bottom: 10px;
        }
        .stat-value {
          font-size: 24px;
          font-weight: bold;
          color: #34017b;
        }
      }
    }
    .quick-access {
      margin-top: 20px;
      .quick-links {
        display: flex;
        gap: 15px;
        .el-button {
          padding: 12px 20px;
          font-size: 14px;
        }
      }
    }
  }
}
</style>