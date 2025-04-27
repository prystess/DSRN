<template>
  <div class="posts-list-container">
    <!-- 列表区：显示所有文章简略信息 -->
    <div class="posts-list">
      <div
        v-for="(post, index) in posts"
        :key="index"
        class="post-item"
        @click="openPostDetail(post)"
      >
        <h3 class="post-title">{{ post.title }}</h3>
        <div class="post-author">{{ post.author }} - {{ post.time }}</div>
        <div class="post-content">
          {{
            post.content.length > 100
              ? post.content.substring(0, 100) + "..."
              : post.content
          }}
        </div>
      </div>
    </div>

    <!-- 模态窗：显示文章详情及操作按钮 -->
    <div
      class="modal-overlay"
      v-if="showPostModal"
      @click.self="closePostModal"
    >
      <div class="post-modal">
        <button class="close-button" @click="closePostModal">×</button>
        <h2 class="modal-title">{{ selectedPost.title }}</h2>
        <div class="modal-author">
          {{ selectedPost.author }} - {{ selectedPost.time }}
        </div>
        <div class="modal-content">{{ selectedPost.content }}</div>
        <div class="modal-actions">
          <button @click="likePost">Like</button>
          <button @click="collectPost">Collect</button>
          <button @click="openCommentModal">Comment</button>
          <button @click="openRewardModal">Reward</button>
        </div>
      </div>
    </div>

    <!-- 评论输入弹窗 -->
    <div
      class="modal-overlay"
      v-if="showCommentModal"
      @click.self="closeCommentModal"
    >
      <div class="post-modal">
        <button class="close-button" @click="closeCommentModal">×</button>
        <h2 class="modal-title">输入评论</h2>
        <textarea
          v-model="commentText"
          placeholder="输入你的评论..."
        ></textarea>
        <button @click="submitComment">提交评论</button>
      </div>
    </div>

    <!-- 打赏输入弹窗 -->
    <div
      class="modal-overlay"
      v-if="showRewardModal"
      @click.self="closeRewardModal"
    >
      <div class="post-modal">
        <button class="close-button" @click="closeRewardModal">×</button>
        <h2 class="modal-title">输入打赏金额</h2>
        <input
          type="number"
          v-model="rewardAmount"
          placeholder="输入打赏金额..."
        />
        <button @click="submitReward">提交打赏</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, defineProps } from "vue";
import useContentManager from "../../composables/useContentManager";
import useAuthorManager from "../../composables/useAuthorManager";

// 接收 posts 数据
const props = defineProps({
  posts: {
    type: Array,
    required: true,
  },
});

const showPostModal = ref(false);
const selectedPost = ref({});
const showCommentModal = ref(false);
const showRewardModal = ref(false);
const commentText = ref("");
const rewardAmount = ref(0);

// 点击某个 post 时打开详情模态窗
const openPostDetail = (post) => {
  selectedPost.value = post;
  showPostModal.value = true;
};

// 关闭详情模态窗
const closePostModal = () => {
  showPostModal.value = false;
};

// 打开评论输入弹窗
const openCommentModal = () => {
  showCommentModal.value = true;
};

// 关闭评论输入弹窗
const closeCommentModal = () => {
  showCommentModal.value = false;
  commentText.value = ""; // 清空输入
};

// 提交评论
const submitComment = async () => {
  try {
    const contentManager = useContentManager();
    const id = selectedPost.value.postID; // 文章 ID
    const comment = commentText.value; // 评论内容
    await contentManager.addComment(id, comment); // 传递评论内容
  } catch (error) {
    console.error("评论操作失败:", error);
  }
  closeCommentModal();
};

// 打开打赏输入弹窗
const openRewardModal = () => {
  showRewardModal.value = true;
};

// 关闭打赏输入弹窗
const closeRewardModal = () => {
  showRewardModal.value = false;
  rewardAmount.value = 0; // 清空输入
};

// 提交打赏
const submitReward = async () => {
  try {
    const content = useContentManager();
    const id = selectedPost.value.postID;

    // 确保 rewardAmount 是一个有效的数字
    const amount = parseFloat(rewardAmount.value);
    if (isNaN(amount) || amount <= 0) {
      throw new Error("请输入有效的打赏金额");
    }

    // 调用 rewardArticle 方法，转换为 Wei
    await content.rewardArticle(id, amount.toString()); // 这里假设合约需要字符串形式的金额

    console.log("打赏金额:", amount);
  } catch (error) {
    console.error("打赏操作失败:", error);
  }
  closeRewardModal();
};

// 以下为各操作的占位函数，具体功能由你来实现
const likePost = async () => {
  try {
    const content = useContentManager();
    const id = selectedPost.value.postID;
    await content.likeArticle(id);
  } catch (error) {
    console.error("点赞操作失败:", error);
  }
};

const collectPost = () => {
  const author = useAuthorManager();
  const account = author.getAccount();
  const id = selectedPost.value.postID;
  author.setCollectArticleId(id, account);
};
</script>

<style scoped>
.posts-list-container {
  padding: 1rem;
}

/* 文章列表样式 */
.posts-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.post-item {
  background-color: #0a0118;
  padding: 1rem;
  border: 1px solid #b429ff;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.post-item:hover {
  background-color: rgba(180, 41, 255, 0.1);
}

.post-title {
  font-size: 1.5rem;
  margin: 0 0 0.5rem 0;
}

.post-author {
  font-size: 0.9rem;
  color: #cccccc;
  margin-bottom: 0.5rem;
}

.post-content {
  font-size: 1rem;
  line-height: 1.4;
}

/* 模态窗样式 */
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

.post-modal {
  background-color: #0a0118;
  border: 1px solid #b429ff;
  border-radius: 8px;
  padding: 2rem;
  width: 90%;
  max-width: 600px;
  color: white;
  position: relative;
}

.close-button {
  position: absolute;
  top: 1rem;
  right: 1rem;
  background: none;
  border: none;
  font-size: 1.5rem;
  color: #b429ff;
  cursor: pointer;
}

.modal-title {
  font-size: 1.8rem;
  margin-bottom: 0.5rem;
}

.modal-author {
  font-size: 0.95rem;
  color: #cccccc;
  margin-bottom: 1rem;
}

.modal-content {
  margin-bottom: 1.5rem;
  line-height: 1.6;
}

.modal-actions {
  display: flex;
  gap: 1rem;
}

.modal-actions button {
  flex: 1;
  background-color: #b429ff;
  border: none;
  border-radius: 4px;
  padding: 0.5rem;
  color: white;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.modal-actions button:hover {
  background-color: #d17cff;
}

/* 输入框样式 */
textarea {
  width: 100%;
  height: 100px;
  border-radius: 4px;
  border: 1px solid #b429ff;
  background-color: #0a0118;
  color: white;
  padding: 0.5rem;
  margin-bottom: 1rem;
}

input[type="number"] {
  width: 100%;
  border-radius: 4px;
  border: 1px solid #b429ff;
  background-color: #0a0118;
  color: white;
  padding: 0.5rem;
  margin-bottom: 1rem;
}
</style>
