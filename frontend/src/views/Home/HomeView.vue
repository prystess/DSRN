<template>
  <div class="home-page">
    <!-- Navigation Bar Component -->
    <NavigationBar />

    <!-- Main Content Area -->
    <div class="main-content">
      <!-- Logo and Tagline -->
      <div class="logo-section">
        <div class="logo">
          <img
            src="../../assets/image/Pasted-20250413-110635_pixian_ai.png"
            alt="Background Logo"
            class="logo-image background-logo"
          />
          <img
            src="../../assets/image/image 2@2x.png"
            alt="DSDN Logo"
            class="logo-image overlay-logo"
          />
        </div>
        <img
          src="../../assets/image/A High-Quality Decentralized Knowledge Hub (1)_pixian_ai.png"
          alt="A High-Quality Decentralized Knowledge Hub"
          class="tagline"
        />
      </div>

      <!-- Search Bar -->
      <div class="search-container">
        <div class="search-bar">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="20"
            height="20"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
            class="search-icon"
          >
            <circle cx="11" cy="11" r="8"></circle>
            <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
          </svg>
          <input
            type="text"
            class="search-input"
            placeholder="Start Consensus..."
            v-model="searchQuery"
            @keyup.enter="handleSearch"
          />
        </div>
      </div>

      <!-- Tab Navigation -->
      <div class="tab-navigation">
        <button
          class="tab-button"
          :class="{ active: activeTab === 'featured' }"
          @click="setActiveTab('featured')"
        >
          Featured Blogs
        </button>
        <button
          class="tab-button"
          :class="{ active: activeTab === 'knowledge' }"
          @click="setActiveTab('knowledge')"
        >
          Knowledge Graph
        </button>
      </div>

      <!-- Featured Blogs Content -->
      <div v-if="activeTab === 'featured'" class="content-container">
        <!-- PostsList 组件展示文章列表 -->
        <PostsList :posts="posts" />
      </div>

      <!-- Knowledge Graph Content -->
      <div v-if="activeTab === 'knowledge'" class="content-container">
        <div class="knowledge-graph">
          <div class="knowledge-header">
            <div class="graph-icon">
              <img
                src="../../assets/image/ai.png"
                alt="ai图谱图标"
                width="50"
                height="50"
              />
            </div>
            <h2 class="knowledge-title">Knowledge Graph</h2>
          </div>
          <div class="graph-items">
            <!-- 修改点击事件为延时2秒再显示 -->
            <div
              v-for="(item, index) in knowledgeItems"
              :key="index"
              class="graph-item"
              @click="delayedShowKnowledgeDetail(item)"
            >
              <div class="item-logo">
                <img :src="item.logo" :alt="item.name" />
              </div>
              <div class="item-name">{{ item.name }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Knowledge Detail Modal (显示大图) -->
    <div class="modal-overlay" v-if="showModal" @click.self="closeModal">
      <div class="knowledge-modal">
        <button class="close-button" @click="closeModal">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            <line x1="18" y1="6" x2="6" y2="18"></line>
            <line x1="6" y1="6" x2="18" y2="18"></line>
          </svg>
        </button>
        <div class="modal-header">
          <div class="modal-logo">
            <img :src="selectedItem.logo" :alt="selectedItem.name" />
          </div>
          <h2 class="modal-title">{{ selectedItem.name }}</h2>
        </div>
        <div class="modal-content">
          <!-- 显示点击后大图 -->
          <img
            :src="selectedItem.fullImage"
            :alt="selectedItem.name"
            class="modal-full-image"
          />
        </div>
      </div>
    </div>

    <!-- 固定在右下角的 Coze 聊天组件容器 -->
    <div id="coze-chat-container"></div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";
import { useRouter } from "vue-router";
import NavigationBar from "../../components/NavigationBar.vue";
import PostsList from "../../components/HomeComponents/PostsListComponent.vue";
import useContentManager from "../../composables/useContentManager";
import useAuthorManager from "../../composables/useAuthorManager";

// 导入知识图谱使用的图标
import BTCLogo from "../../assets/image/BTC.png";
import ETHLogo from "../../assets/image/ETH.png";
import SuiLogo from "../../assets/image/Sui.png";
import AptosLogo from "../../assets/image/Aptos.png";

// 导入点击后要显示的大图（例如 BTC 点击后显示的图片）
import btcFull from "../../assets/staticAtlas/btc.png";
import ethFull from "../../assets/staticAtlas/eth.png";
import suiFull from "../../assets/staticAtlas/sui.png";
import aptosFull from "../../assets/staticAtlas/aptos.png";

const router = useRouter();
const activeTab = ref("featured");
const searchQuery = ref("");
const showModal = ref(false);
const selectedItem = reactive({
  name: "",
  logo: "",
  fullImage: "",
});

// posts 保存 Featured Blogs 中的文章数据
const posts = ref([]);

// 定义知识图谱数据，包含图标和点击后显示的大图
const knowledgeItems = ref([
  {
    name: "BTC",
    logo: BTCLogo,
    fullImage: btcFull,
  },
  {
    name: "ETH",
    logo: ETHLogo,
    fullImage: ethFull,
  },
  {
    name: "Sui",
    logo: SuiLogo,
    fullImage: suiFull,
  },
  {
    name: "Aptos",
    logo: AptosLogo,
    fullImage: aptosFull,
  },
]);

// 设置 Tab 切换
const setActiveTab = (tab) => {
  activeTab.value = tab;
};

// 处理搜索：点击回车后跳转到搜索页（仅作示例）
const handleSearch = () => {
  if (searchQuery.value.trim()) {
    router.push({ name: "search", query: { q: searchQuery.value } });
  }
};

// 直接显示模态框
const showKnowledgeDetail = (item) => {
  selectedItem.name = item.name;
  selectedItem.logo = item.logo;
  selectedItem.fullImage = item.fullImage;
  showModal.value = true;
};

// 延时2秒后显示模态框，用于知识图谱项点击
const delayedShowKnowledgeDetail = (item) => {
  setTimeout(() => {
    showKnowledgeDetail(item);
  }, 2000);
};

const closeModal = () => {
  showModal.value = false;
};

onMounted(async () => {
  // 获取 Featured Blogs 数据：调用合约接口获取文章详情
  const content = useContentManager();
  const author = useAuthorManager();
  if (!content) {
    console.error("Web3 初始化失败");
    return;
  }
  try {
    const account = await content.getAccount();
    if (!account) {
      console.error("未连接钱包");
      return;
    }
    const postIDs = await content.getAllArticleId();
    posts.value = [];
    for (let i = 0; i < postIDs.length; i++) {
      const articleDetail = await content.getArticleDetails(postIDs[i]);
      const authorAddress = articleDetail.author;
      const authorInfo = await author.getAuthorInformationByAddress(
        authorAddress
      );
      const authorName = authorInfo.authorName || "Unknown";
      posts.value.push({
        title: articleDetail.title || "Topic",
        author: authorName,
        time: "2025-4-12 10:56", // 示例数据
        content: articleDetail.content,
        postID: articleDetail.id,
      });
    }
  } catch (error) {
    console.error("获取文章信息失败:", error);
  }

  // Coze 聊天组件集成（固定在右下角）
  const script = document.createElement("script");
  script.src =
    "https://lf-cdn.coze.cn/obj/unpkg/flow-platform/chat-app-sdk/1.2.0-beta.6/libs/cn/index.js";
  script.onload = () => {
    new window.CozeWebSDK.WebChatClient({
      config: { bot_id: "7493140079997632547" },
      componentProps: { title: "Coze" },
      auth: {
        type: "token",
        token:
          "pat_plkUl7cURBsOMzutTbURAzT4QZlwtsxqXOltw4NeHNnmzh1BoBNFbAUft2GPiQYP",
        onRefreshToken: function () {
          return "pat_plkUl7cURBsOMzutTbURAzT4QZlwtsxqXOltw4NeHNnmzh1BoBNFbAUft2GPiQYP";
        },
      },
      container: "#coze-chat-container",
    });
  };
  document.body.appendChild(script);
});
</script>

<style scoped>
/* 主页基本布局 */
.home-page {
  min-height: 100vh;
  background-color: #0a0118;
  color: white;
  display: flex;
  flex-direction: column;
  margin: 0;
  padding: 0;
}
.main-content {
  flex: 1;
  padding: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* Logo Section */
.logo-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 2rem;
}
.logo {
  position: relative;
  width: 300px;
  height: 300px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 60px;
  margin-bottom: 1rem;
}
.logo-image {
  width: 100%;
  height: 100%;
  object-fit: contain;
}
.background-logo {
  position: absolute;
  top: 5%;
  left: 50%;
  transform: translate(-50%, 0) scale(2.3);
  z-index: 1;
  opacity: 1;
  pointer-events: none;
}
.overlay-logo {
  position: absolute;
  z-index: 2;
  transform: scale(0.8);
  animation: float 3s ease-in-out infinite;
}
@keyframes float {
  0% {
    transform: translateY(0px) scale(0.8);
  }
  50% {
    transform: translateY(-10px) scale(0.8);
  }
  100% {
    transform: translateY(0px) scale(0.8);
  }
}
.tagline {
  margin-top: 100px;
  font-size: 2rem;
  font-weight: bold;
  text-align: center;
  color: #b429ff;
  letter-spacing: 2px;
  max-width: 800px;
}

/* Search Container */
.search-container {
  width: 100%;
  max-width: 500px;
  margin-bottom: 2rem;
}
.search-bar {
  display: flex;
  align-items: center;
  background-color: transparent;
  border: 2px solid #b429ff;
  border-radius: 25px;
  padding: 0.5rem 1rem;
}
.search-icon {
  color: #b429ff;
  margin-right: 0.5rem;
}
.search-input {
  flex: 1;
  background: transparent;
  border: none;
  color: white;
  font-size: 1rem;
  outline: none;
}

/* Tab Navigation */
.tab-navigation {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
}
.tab-button {
  position: relative;
  z-index: 0;
  border: none;
  border-radius: 25px;
  padding: 0.5rem 1.5rem;
  font-size: 1rem;
  color: white;
  background-color: transparent;
  cursor: pointer;
  transition: all 0.2s ease;
}
.tab-button::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 25px;
  padding: 2px;
  background: linear-gradient(
    90deg,
    #5e02c7,
    #7902ce,
    #5a26e8,
    #fcf1fe,
    #df01ea
  );
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  z-index: -1;
}
.tab-button.active::before {
  background: linear-gradient(white, white);
}
.tab-button.active {
  background-color: transparent;
  color: white;
}
.tab-button:hover {
  background-color: rgba(180, 41, 255, 0.1);
}
.tab-button:hover::before {
  filter: brightness(1.2);
}

/* Content Container */
.content-container {
  width: 100%;
  max-width: 800px;
  padding: 1.5rem;
  border-radius: 10px;
  border: 1px solid transparent;
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

/* Knowledge Graph Styles */
.knowledge-header {
  display: flex;
  align-items: center;
  margin-bottom: 2rem;
}
.graph-icon {
  margin-right: 0.5rem;
}
.knowledge-title {
  font-size: 1.5rem;
  font-weight: bold;
  color: white;
  margin: 0;
}
.graph-items {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  max-height: 60vh;
  overflow-y: auto;
}
.graph-item {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1.5rem;
  border-radius: 10px;
  border: 1px solid transparent;
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(
        90deg,
        #df01ea 0%,
        #5e02c7 25%,
        #0a0118 50%,
        #5e02c7 75%,
        #df01ea 100%
      )
      border-box;
  cursor: pointer;
  transition: all 0.3s ease;
}
.graph-item:hover {
  box-shadow: 0 0 15px rgba(223, 1, 234, 0.6), 0 0 30px rgba(223, 1, 234, 0.3);
  transform: translateY(-2px);
}
.item-logo {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;
  margin-right: 1rem;
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
}
.item-logo img {
  width: 80%;
  height: 80%;
  object-fit: contain;
}
.item-name {
  font-size: 1.2rem;
  font-weight: bold;
  color: white;
}

/* Modal Styles (Knowledge Detail) */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
.knowledge-modal {
  background-color: #0a0118;
  border: 1px solid #b429ff;
  border-radius: 10px;
  padding: 2rem;
  width: 90%;
  max-width: 600px;
  position: relative;
}
.close-button {
  position: absolute;
  top: 1rem;
  right: 1rem;
  background: none;
  border: none;
  color: #b429ff;
  cursor: pointer;
  padding: 0;
}
.modal-header {
  display: flex;
  align-items: center;
  margin-bottom: 1.5rem;
}
.modal-logo {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  overflow: hidden;
  margin-right: 1rem;
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal-logo img {
  width: 80%;
  height: 80%;
  object-fit: contain;
}
.modal-title {
  font-size: 1.5rem;
  font-weight: bold;
  margin: 0;
}
.modal-content {
  text-align: center;
}
.modal-full-image {
  width: 100%;
  height: auto;
  border-radius: 10px;
}

/* 固定右下角 Coze 聊天组件容器 */
#coze-chat-container {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 1100;
}
</style>
