<template>
  <div class="profile-page">
    <!-- Navigation Bar Component -->
    <NavigationBar />

    <!-- Main Content Area -->
    <div class="main-content">
      <!-- Left Sidebar -->
      <div class="sidebar">
        <!-- User Profile Section -->
        <div class="user-profile">
          <div class="avatar-container">
            <img :src="userProfile.avatar" alt="User Avatar" class="avatar" />
          </div>
          <div class="username">{{ userProfile.username }}</div>
          <button class="edit-button" @click="openEditModal">Edit</button>
        </div>

        <!-- Sidebar Navigation -->
        <div class="sidebar-nav">
          <button
            v-for="(item, index) in sidebarItems"
            :key="index"
            class="sidebar-item"
            :class="{ active: activeSection === item.id }"
            @click="setActiveSection(item.id)"
          >
            <span class="item-icon" v-if="item.icon">
              <component :is="item.icon" />
            </span>
            <span class="item-name">{{ item.name }}</span>
            <!-- 包裹文字 -->
          </button>
        </div>
      </div>

      <!-- Content Area -->
      <div class="content-area">
        <!-- My Posts Section -->
        <ScrollableContainer
          v-if="activeSection === 'mypost'"
          :totalItems="posts.length"
        >
          <template #icon>
            <img
              src="../../assets/image/post.png"
              alt="My Post Icon"
              style="width: 50px; height: 50px"
            />
          </template>

          <template #title>
            <img
              src="../../assets/image/my post.png"
              alt="My Post Title"
              style="height: 50px"
            />
          </template>

          <PostItem
            v-for="(post, index) in posts"
            :key="index"
            :title="post.title"
            :content="post.content"
            :time="post.time"
            @delete="deletePost(index)"
          />
        </ScrollableContainer>

        <!-- Assets Section -->
        <ScrollableContainer
          v-if="activeSection === 'assets'"
          :showTotal="false"
        >
          <template #icon>
            <img
              src="../../assets/image/my asserts.png"
              alt=" Assets Icon"
              style="width: 50px; height: 50px"
            />
          </template>

          <template #title>
            <img
              src="../../assets/image/assets.png"
              alt="My Assets"
              style="height: 50px"
            />
          </template>

          <div class="assets-content">
            <div class="assets-total">
              <h3>Total Assets In Dao</h3>
              <div class="assets-amount">
                {{ stakingTotalAssets }} <span>KLT Tokens</span>
              </div>
            </div>

            <div class="assets-staking">
              <h3>Staking Assets</h3>
              <div class="assets-amount">
                {{ stakingAssets }} <span>KLT Tokens</span>
              </div>
            </div>

            <div class="assets-divider"></div>

            <div class="assets-detail-header">
              <h3>Assets Detail</h3>
            </div>

            <div class="assets-detail-list">
              <AssetDetailItem
                v-for="(asset, index) in assetDetails"
                :key="index"
                :title="asset.title"
                :amount="asset.amount"
                @details="showAssetDetails(asset)"
              />
            </div>
          </div>
        </ScrollableContainer>

        <!-- Collection Section -->
        <ScrollableContainer
          v-if="activeSection === 'collection'"
          :totalItems="collections.length"
        >
          <template #icon>
            <img
              src="../../assets/image/collection.png"
              alt=" collection Icon"
              style="width: 65px; height: 60px"
            />
          </template>

          <template #title>
            <img
              src="../../assets/image/my collection.png"
              alt="My collection"
              style="height: 55px"
            />
          </template>

          <div
            v-for="(collection, index) in collections"
            :key="index"
            class="collection-item"
          >
            <h3 class="collection-title">{{ collection.title }}</h3>
            <div class="collection-author">
              {{ collection.author }} {{ collection.time }}
            </div>
            <p class="collection-content">{{ collection.content }}</p>
            <p class="collection-tag"># tag</p>
            <div class="divider" v-if="index !== collections.length - 1"></div>
          </div>
        </ScrollableContainer>

        <!-- Notification Section -->
        <ScrollableContainer
          v-if="activeSection === 'notification'"
          :totalItems="notifications.length"
        >
          <template #icon>
            <img
              src="../../assets/image/notification.png"
              alt=" notification Icon"
              style="width: 70px; height: 60px"
            />
          </template>

          <template #title>
            <img
              src="../../assets/image/my notification.png"
              alt="My notification"
              style="height: 55px"
            />
          </template>

          <notificateComponent
            v-for="(notification, index) in notifications"
            :key="index"
            :type="notification.type"
            :avatar="notification.avatar"
            :content="notification.content"
            :comment="notification.comment"
          />
        </ScrollableContainer>

        <!-- Followers Section -->
        <ScrollableContainer
          v-if="activeSection === 'followers'"
          :totalItems="followers.length"
        >
          <template #icon>
            <img
              src="../../assets/image/followers.png"
              alt=" Followers Icon"
              style="width: px; height: 55px"
            />
          </template>

          <template #title>
            <img
              src="../../assets/image/myfollowers.png"
              alt="my Followers"
              style="height: 60px"
            />
          </template>

          <followerComponent
            v-for="(follower, index) in followers"
            :key="index"
            :avatar="follower.avatar"
            :name="follower.name"
            @visit-homepage="visitHomepage(follower.id)"
          />
        </ScrollableContainer>

        <!-- Following Section -->
        <ScrollableContainer
          v-if="activeSection === 'following'"
          :totalItems="following.length"
        >
          <template #icon>
            <img
              src="../../assets/image/following.png"
              alt=" following Icon"
              style="width: 50px; height: 50px"
            />
          </template>

          <template #title>
            <img
              src="../../assets/image/following.png"
              alt="my followin"
              style="height: 55px"
            />
          </template>

          <followerComponent
            v-for="(user, index) in following"
            :key="index"
            :avatar="user.avatar"
            :name="user.name"
            @visit-homepage="visitHomepage(user.id)"
          />
        </ScrollableContainer>
      </div>
    </div>

    <!-- Edit Profile Modal -->
    <div
      class="modal-overlay"
      v-if="showEditModal"
      @click.self="closeEditModal"
    >
      <div class="edit-modal">
        <!-- Close Button -->
        <button class="close-button" @click="closeEditModal">
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

        <!-- Avatar Upload -->
        <div class="modal-avatar-container">
          <img
            :src="editFormData.avatar"
            alt="User Avatar"
            class="modal-avatar"
          />
          <button class="upload-button" @click="triggerFileUpload">
            Upload
          </button>
          <input
            type="file"
            ref="fileInput"
            @change="handleFileUpload"
            accept="image/*"
            style="display: none"
          />
        </div>

        <!-- Form Fields -->
        <div class="form-group">
          <label for="username">Username:</label>
          <input
            type="text"
            id="username"
            v-model="editFormData.username"
            class="form-input"
          />
        </div>

        <div class="form-group">
          <label for="self-intro">Self-Introduction:</label>
          <textarea
            id="self-intro"
            v-model="editFormData.selfIntro"
            class="form-textarea"
          ></textarea>
        </div>

        <!-- Save Button -->
        <button class="save-button" @click="saveProfile">SAVE</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";
import NavigationBar from "../../components/NavigationBar.vue";
import PostItem from "../../components/ProfileComponents/myPostComponent.vue";
import AssetDetailItem from "../../components/ProfileComponents/assetComponent.vue";
import ScrollableContainer from "../../components/ProfileComponents/scrollableContainer.vue";
import notificateComponent from "../../components/ProfileComponents/notificateComponent.vue";
import followerComponent from "../../components/ProfileComponents/followerComponent.vue";
import HomepageComponent from "../../components/HomeComponents/HomepageComponent.vue";

import useKLT from "../../composables/useKLT";
import useAuthorManager from "../../composables/useAuthorManager";
import useContentManager from "../../composables/useContentManager";
import useContentReviewDAO from "../../composables/useContentReviewDAO";

import authorAvatar from "../../assets/image/author1.png";
import authorAvatar1 from "../../assets/image/author2.png";
import authorAvatar2 from "../../assets/image/author3.png";
import authorAvatar3 from "../../assets/image/jiaxu.png";

const authorInfo = ref(null);
// 用户所有的资产
const userTotalAssets = ref(0);
// 用户质押的资产
const stakingAssets = ref(0);
// 池子中所有的资产
const stakingTotalAssets = ref(0);
// 文章的奖励总和
const contentRewards = ref(0);

const AuthorRewards = ref(0);

const posts = ref([]);
const collections = ref([]);
const followers = ref([]);
const following = ref([]);

onMounted(async () => {
  const klt = useKLT();

  const author = useAuthorManager();

  const content = useContentManager();

  const dao = useContentReviewDAO();

  if (!author && !content) {
    console.error("Web3 初始化失败");
    return;
  }

  try {
    const account = await author.getAccount();
    if (!account) {
      console.error("未连接钱包");
      return;
    }
    // author
    const authorInfo = await author.getAuthorInformationByAddress(account);

    userProfile.username = authorInfo[0];

    //作者的收藏
    collections.value = [];
    const collectionIDs = authorInfo[6];
    for (let index = 0; index < collectionIDs.length; index++) {
      const element = await content.getArticleDetails(collectionIDs[index]);
      console.log(element[1] + "我在这里呀");
      collections.value.push({
        title: "Topic",
        author: "author",
        time: "2025-4-12 10:56",
        content: element[1],
      });
    }

    //作者的关注
    following.value = [];
    const followingAddresses = authorInfo[2];
    console.log(followingAddresses);
    for (let index = 0; index < followingAddresses.length; index++) {
      const followingDetail = await author.getAuthorInformationByAddress(
        followingAddresses[index]
      );
      following.value.push({
        id: followingDetail[1],
        name: followingDetail[0],
        avatar: "/avatar-placeholder.png",
      });
    }

    // // 作者的粉丝
    followers.value = [];
    const followersAddresses = authorInfo[3];
    for (let index = 0; index < followersAddresses.length; index++) {
      const followerDetail = await author.getAuthorInformationByAddress(
        followersAddresses[index]
      );
      followers.value.push({
        id: followerDetail[1],
        name: followerDetail[0],
        avatar: "/avatar-placeholder.png",
      });
    }

    // content
    const contensIDs = await author.getArticlesForAuthor(account);
    posts.value = [];
    for (let i = 0; i < contensIDs.length; i++) {
      // 调用 content 合约中的 getArticleDetails，传入对应的 ID
      const articleDetail = await content.getArticleDetails(contensIDs[i]);
      const reward = Number(articleDetail.reward); // 将 BigInt 转换为普通数字
      contentRewards.value += reward;
      // 构造用于显示的文章对象
      posts.value.push({
        title: "Topic", // 固定值
        time: "2025-4-12 10:56", // 固定值
        content: articleDetail.content, // 从合约返回的内容字段
      });
    }

    // dao And KLT
    stakingTotalAssets.value = await klt.balanceOf(
      "0xc4F125A67879492946B2fD3B0d87b4458F4CF513"
    );
    // 用户所有的资产
    userTotalAssets.value = await klt.balanceOf(account);
    // 用户质押的资产
    stakingAssets.value = await dao.getStakeOf(account);
    const rawStakeAmount = stakingAssets.value;
    stakingAssets.value = (Number(rawStakeAmount) / 1e18).toFixed(2);

    const amount = contentRewards.value;
    contentRewards.value = (Number(amount) / 1e18).toFixed(2);
  } catch (error) {
    console.error("获取作者信息失败:", error);
  }

  // DAO
});

// Active section state
const activeSection = ref("mypost");

// 查询名称功能

/////////////////////////////////////////////
// User profile data
const userProfile = reactive({
  username: "Autor",
  avatar: authorAvatar1,
  selfIntro: "This is my self introduction.",
});

// Edit modal state
const showEditModal = ref(false);
const fileInput = ref(null);
const editFormData = reactive({
  username: "",
  avatar: "",
  selfIntro: "",
});

// Sidebar items
const sidebarItems = [
  { id: "mypost", name: "My Post", icon: null },
  { id: "assets", name: "Assets", icon: null },
  { id: "collection", name: "Collection", icon: null },
  { id: "notification", name: "Notification", icon: null },
  { id: "following", name: "Following", icon: null },
  { id: "followers", name: "Followers", icon: null },
];

// Asset details data

const assetDetails = ref([
  { title: "Reviewer-total-assets", amount: userTotalAssets },
  { title: "Reviewer-Rewards", amount: contentRewards },
  { title: "Reviewer-Forfeiture", amount: 0 },
  { title: "Reader-Report Rewards", amount: contentRewards.value * 0.9 },
  { title: "Author-Rewards From Readers", amount: AuthorRewards },
]);

// Notification data
const notifications = ref([
  {
    type: "comment",
    avatar: authorAvatar,
    content: "dusko commented on your post",
    comment: "comments",
  },
  {
    type: "like",
    avatar: authorAvatar1,
    content: "pzqy liked your post",
  },
  {
    type: "collect",
    avatar: authorAvatar2,
    content: "d50 Collected your post",
  },
  {
    type: "reward",
    avatar: authorAvatar3,
    content: "jiaxu gave you a 1 tokens reward for your post",
  },
]);

// Function to set active section
const setActiveSection = (sectionId) => {
  activeSection.value = sectionId;
};

// Function to open edit modal
const openEditModal = () => {
  // Copy current profile data to form
  editFormData.username = userProfile.username;
  editFormData.avatar = userProfile.avatar;
  editFormData.selfIntro = userProfile.selfIntro;
  showEditModal.value = true;
};

// Function to close edit modal
const closeEditModal = () => {
  showEditModal.value = false;
};

// Function to trigger file upload dialog
const triggerFileUpload = () => {
  fileInput.value.click();
};

// Function to handle file upload
const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    // Create a URL for the file
    const fileURL = URL.createObjectURL(file);
    editFormData.avatar = fileURL;
  }
};

// Function to save profile changes
const saveProfile = () => {
  // Update user profile with form data
  userProfile.username = editFormData.username;
  userProfile.avatar = editFormData.avatar;
  userProfile.selfIntro = editFormData.selfIntro;

  // Close the modal
  closeEditModal();

  console.log("Profile updated:", userProfile);
};

// Function to visit user's homepage
const visitHomepage = (userId) => {
  console.log(`Visit homepage of user ${userId}`);
};

// Function to delete post
const deletePost = (index) => {
  posts.value.splice(index, 1);
};

// Function to show asset details
const showAssetDetails = (asset) => {
  console.log("Show details for:", asset.title);
  // Implement your details logic here
};
</script>

<style scoped>
.profile-page {
  min-height: 100vh;
  height: 100vh; /* Fixed height to prevent page scrolling */
  background-color: #0a0118;
  color: white;
  display: flex;
  flex-direction: column;
  overflow: hidden; /* Prevent page scrolling */
}

.main-content {
  display: flex;
  flex: 1;
  overflow: hidden; /* Prevent main content from scrolling */
}

/* Sidebar Styles */
.sidebar {
  width: 200px;
  min-width: 200px; /* Prevent sidebar from shrinking */
  background-color: #0a0118;
  border-right: 1px solid rgba(180, 41, 255, 0.3);
  display: flex;
  flex-direction: column;
  padding: 1.5rem 0;
}

.user-profile {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 2rem;
}

.avatar-container {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  overflow: hidden;
  margin-bottom: 0.5rem;
  border: 2px solid white;
}

.avatar {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.username {
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
}

.edit-button {
  border: 1px solid transparent; /* 透明边框 */
  border-radius: 20px;
  padding: 0.3rem 1.5rem;
  cursor: pointer;
  color: #b429ff;
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  transition: all 0.2s ease;
}

.edit-button:hover {
  background-color: rgba(180, 41, 255, 0.1);
}

.sidebar-nav {
  display: flex;
  flex-direction: column;
}

.sidebar-item {
  display: flex;
  align-items: center;
  padding: 0.8rem 1.5rem;
  background-color: transparent;
  border: none;
  color: white;
  text-align: left;
  cursor: pointer;
  transition: all 0.2s ease;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.sidebar-item:hover {
  background-color: rgba(180, 41, 255, 0.1);
}

.sidebar-item.active {
  background-color: rgba(180, 41, 255, 0.2);
  color: #b429ff;
  border-left: 3px solid #b429ff;
}

.item-icon {
  margin-right: 0.5rem;
}

/* Content Area Styles */
.content-area {
  flex: 1;
  padding: 1.5rem;
  overflow: hidden;
}

/* Assets specific styles */
.assets-content {
  padding: 1rem 0;
}

.assets-total,
.assets-staking {
  margin-bottom: 1.5rem;
}

.assets-total h3,
.assets-staking h3,
.assets-detail-header h3 {
  color: #b429ff;
  font-size: 1.2rem;
  margin-bottom: 0.5rem;
}

.assets-amount {
  font-size: 2rem;
  font-weight: bold;
}

.assets-amount span {
  font-size: 1.2rem;
  margin-left: 0.5rem;
}

.assets-divider {
  height: 1px;
  background-color: rgba(180, 41, 255, 0.3);
  margin: 1.5rem 0;
}

.assets-detail-header {
  margin-bottom: 1.5rem;
}

.assets-detail-list {
  display: flex;
  flex-direction: column;
}

/* Collection specific styles */
.collection-item {
  padding: 1rem 0;
}

.collection-title {
  font-size: 1.2rem;
  margin: 0 0 0.5rem 0;
}

.collection-author {
  color: #ccc;
  font-size: 0.9rem;
  margin-bottom: 0.5rem;
}

.collection-content {
  margin: 0.5rem 0;
  line-height: 1.5;
}

.collection-tag {
  color: #b429ff;
  margin: 0.5rem 0 0;
}

.divider {
  height: 1px;
  background-color: #b429ff;
  margin: 1rem 0;
}

/* Modal Styles */
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

.edit-modal {
  width: 90%;
  max-width: 500px; /* 保持原弹窗宽度 */
  padding: 2rem; /* 保持原内边距 */
  border-radius: 10px; /* 保持原圆角 */
  border: 1px solid transparent;
  position: relative; /* 保持定位属性 */

  /* 渐变边框 + 背景透明 */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;

  /* 玻璃模糊质感 */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
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

.modal-avatar-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 2rem;
}

.modal-avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid white;
  margin-bottom: 1rem;
}

.upload-button {
  border: 1px solid transparent;
  border-radius: 10px;
  padding: 0.5rem 1.5rem;
  cursor: pointer;
  color: white;
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  transition: all 0.2s ease, transform 0.1s ease;
  font-size: 1rem;
  letter-spacing: 0.05em;
}

/* 悬停时：发光，立体感更强 */
.upload-button:hover {
  transform: translateY(-1px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
}

/* 按下时：下沉，内阴影模拟按压效果 */
.upload-button:active {
  transform: translateY(2px);
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.5),
    inset 0 1px 3px rgba(0, 0, 0, 0.3);
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: bold;
}

.form-input,
.form-textarea {
  width: 100%;
  padding: 0.8rem;
  border: 1px solid rgba(82, 4, 80, 0.2);
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.05);
  color: white;
  font-size: 1rem;
  transform: translateX(-14px);
}

.form-textarea {
  min-height: 150px;
  resize: vertical;
}

.save-button {
  display: block;
  width: 50%;
  margin: 0 auto; /* 保持居中位置不变 */
  border: 1px solid transparent;
  border-radius: 20px;
  padding: 0.8rem;
  font-size: 1.2rem;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 2px;
  color: white;

  /* 渐变边框 + 背景透明 */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;

  /* 玻璃模糊质感 */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);

  transition: all 0.2s ease, transform 0.1s ease;
}

/* 悬停：发光+浮起立体感 */
.save-button:hover {
  transform: translateY(-1px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
}

/* 按下：下沉，按压效果 */
.save-button:active {
  transform: translateY(2px);
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.5),
    inset 0 1px 3px rgba(0, 0, 0, 0.3);
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .main-content {
    flex-direction: column;
  }
  @font-face {
    font-family: "SFProDisplayBold"; /* 自定义字体名称 */
    src: url("/src/assets/font/SFPRODISPLAYMEDIUM.OTF") format("opentype"); /* 字体文件路径 */
    font-weight: bold; /* 字体粗细 */
    font-style: normal; /* 字体样式 */
  }

  .sidebar {
    width: 100%;
    min-width: 100%;
    border-right: none;
    border-bottom: 1px solid rgba(180, 41, 255, 0.3);
    padding: 1rem 0;
  }

  .user-profile {
    margin-bottom: 1rem;
  }

  .avatar-container {
    width: 80px;
    height: 80px;
  }

  .sidebar-nav {
    flex-direction: row;
    overflow-x: auto;
    padding: 0 1rem;
  }

  .sidebar-item {
    padding: 0.5rem 1rem;
    border-bottom: none;
    border-right: 1px solid rgba(255, 255, 255, 0.1);
  }

  .content-area {
    padding: 1rem;
  }

  .edit-modal {
    width: 95%;
    padding: 1.5rem;
  }
}
.item-name {
  font-size: 1rem; /* 设置字体大小 */
  font-weight: bold; /* 使用 bold 字体粗细 */
  font-family: "SFProDisplay", sans-serif; /* 使用自定义字体 */
  color: #ffffff; /* 设置字体颜色 */
  text-transform: uppercase; /* 可选：大写字母 */
  letter-spacing: 1px; /* 可选：字母间距 */
}
</style>
