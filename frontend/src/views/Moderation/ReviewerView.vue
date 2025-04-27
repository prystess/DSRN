<template>
  <div class="reviewer-dashboard">
    <!-- Navigation Bar Component -->
    <NavigationBar />

    <!-- Main Content Area -->
    <div v-if="activeView === 'dashboard'" class="main-content">
      <!-- Welcome Section -->
      <div class="welcome-section">
        <div class="crown-icon">
          <img
            src="../../assets/image/author2.png"
            alt="Crown"
            class="crown-img"
          />
        </div>
        <h1 class="welcome-text">WELCOME BACK, REVIEWER!</h1>
      </div>

      <!-- Stake Tokens Section -->
      <div class="action-section">
        <button class="stake-button" @click="showStakeModal = true">
          Stake tokens
        </button>
      </div>

      <!-- Review Posts Section -->
      <div class="review-section">
        <h2 class="review-heading">
          There's <span class="post-count">{{ pendingPostsCount }}</span> posts
          waiting for you to review!
        </h2>
        <button class="review-button" @click="activeView = 'reviewPosts'">
          Review Posts
        </button>
      </div>

      <!-- Voting Section -->
      <div class="voting-section">
        <h2 class="voting-heading">
          Auditor elections are live! You've earned your voting rights — don't
          miss the chance to vote!
        </h2>
        <button class="voting-button" @click="activeView = 'voting'">
          Participate In Voting
        </button>
      </div>
    </div>

    <!-- Review Posts View -->
    <div v-if="activeView === 'reviewPosts'" class="content-container">
      <!-- Back Button -->
      <button class="back-button" @click="activeView = 'dashboard'">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="40"
          height="40"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <path d="m15 18-6-6 6-6" />
        </svg>
      </button>

      <div class="posts-container">
        <div class="posts-list">
          <PostItem
            v-for="(post, index) in noreleaseContens"
            :key="index"
            :title="post.title"
            :content="post.content"
            :time="post.time"
            @delete="reviewPost(post, index)"
          />
        </div>
      </div>
    </div>

    <!-- Post Detail Review View -->
    <div
      v-if="activeView === 'postDetail'"
      class="content-container post-review-view"
    >
      <!-- Back Button -->
      <button class="back-button" @click="activeView = 'reviewPosts'">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="40"
          height="40"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <path d="m15 18-6-6 6-6" />
        </svg>
      </button>

      <div class="post-detail-wrapper">
        <div class="post-detail-container">
          <div class="post-detail-content">
            <div class="post-content-inner">
              <div
                v-if="currentPost.content"
                v-html="formatPostContent(currentPost.content)"
              ></div>
              <div v-else class="sample-content">
                <p class="post-heading">
                  ### **Bitcoin Products Tailored for Institutional Demand**
                </p>
                <p>
                  Rochard explained that the company's target clients include
                  **credit allocators seeking volatility protection** and
                  **equity risk-takers pursuing Bitcoin's asymmetric upside**.
                  Market conditions permitting, its long-term vision involves
                  securing $1 trillion in BTC over the next 21 years.
                </p>

                <p>
                  When asked about the timing and motivation behind the launch,
                  Rochard revealed that the concept of a Bitcoin-backed
                  securitization firm had intrigued him since his early days in
                  Bitcoin, aligning with his background in asset-backed finance.
                </p>

                <p>
                  He noted that Donald Trump's recent presidential election
                  victory catalyzed the plan, signaling a regulatory shift:
                </p>
                <p>
                  > "Moving forward, the SEC will operate neutrally, free from
                  political influence, ensuring balanced oversight for Bitcoin
                  financial products to safeguard U.S. capital markets. This
                  clarity gives established institutions the confidence to
                  engage constructively with Bitcoin."
                </p>

                <p>
                  Rochard emphasized that his vision is to expand Bitcoin's
                  utility by packaging it into **structured financial
                  instruments** that meet institutional standards for
                  transparency, regulation, and risk management. This approach
                  aligns with broader trends in institutional crypto-native
                  products, including exchange-traded products (ETPs) and
                  asset-backed notes.
                </p>

                <p>
                  The company's mission, per its announcement, is to "forge
                  enduring partnerships between credit allocators and
                  risk-takers, unlocking value for capital markets through
                  Bitcoin-backed structured financing that offers transparent,
                  regulated, and efficient risk transfer for global strategic
                  reserve assets."
                </p>

                <p>---</p>

                <p class="post-heading">
                  ### **Validation from Bitcoin ETF Success**
                </p>
                <p>
                  Rochard highlighted the explosive success of Bitcoin ETFs as
                  proof of market demand, calling them "the most successful
                  product launch in financial industry history." He argued that
                  while institutional investors are constrained by volatility,
                  risk-seeking players crave leverage—a gap his firm aims to
                  bridge:
                </p>
                <p>
                  > "The Bitcoin Bond Company exists to responsibly connect
                  these two groups through Bitcoin-backed products, creating
                  long-term value for both."
                </p>
              </div>
            </div>
          </div>
        </div>

        <div class="post-actions">
          <div class="button-group">
            <button class="pass-button" @click="passFunction">
              <span>PASS</span>
            </button>
            <button class="reject-button" @click="rejectFunction">
              <span>REJECT</span>
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Voting View -->
    <div v-if="activeView === 'voting'" class="content-container">
      <!-- Back Button -->
      <button class="back-button" @click="activeView = 'dashboard'">
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
          <path d="m15 18-6-6 6-6" />
        </svg>
      </button>

      <div class="voting-container">
        <div class="voting-header">
          <div class="participant-header">Participant</div>
          <div class="staked-header">Staked Token Amount</div>
          <div class="info-header">Information</div>
          <div class="option-header">Option</div>
        </div>

        <div class="voting-list">
          <div v-for="(voter, index) in voters" :key="index" class="voter-item">
            <div class="voter-info">
              <div class="voter-avatar">
                <img src="../../assets/image/author1.png" :alt="voter.id" />
              </div>
              <div class="voter-id">{{ voter.id }}</div>
            </div>

            <div class="voter-tokens">{{ voter.tokens }} Tokens</div>

            <div class="voter-info-link">
              <button class="homepage-button" @click="visitHomepage(voter.id)">
                Homepage
              </button>
            </div>

            <div class="voter-options">
              <button
                class="approve-button"
                :class="{ active: voter.isApproved === true }"
                @click="approveVoter(index)"
              >
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
                >
                  <path d="M20 6L9 17l-5-5" />
                </svg>
              </button>

              <button
                class="reject-button"
                :class="{ active: voter.isApproved === false }"
                @click="rejectVoter(index)"
              >
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
                >
                  <circle cx="12" cy="12" r="10" />
                  <line x1="4.93" y1="4.93" x2="19.07" y2="19.07" />
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Stake Modal -->
    <div
      class="modal-overlay"
      v-if="showStakeModal"
      @click.self="showStakeModal = false"
    >
      <div class="modal">
        <div class="modal-header">
          <h2>Stake Tokens</h2>
          <button class="close-modal-button" @click="showStakeModal = false">
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
        </div>

        <div class="modal-content">
          <div class="form-group">
            <label for="stakeAmount">Amount to stake:</label>
            <input
              type="number"
              id="stakeAmount"
              v-model="stakeAmount"
              class="form-input"
              min="100"
              placeholder="Enter amount"
            />
            <p class="form-help">Minimum 100 tokens required</p>
          </div>

          <div class="token-balance">
            <p>
              Your current balance: <span>{{ userTokens }}</span> Tokens
            </p>
          </div>
        </div>

        <div class="modal-footer">
          <button class="cancel-button" @click="showStakeModal = false">
            Cancel
          </button>
          <button class="submit-button" @click="stakeTokens">Stake</button>
        </div>
      </div>
    </div>

    <!-- Pass Modal -->
    <div
      class="modal-overlay"
      v-if="showPassModal"
      @click.self="showPassModal = false"
    >
      <div class="modal">
        <div class="modal-header">
          <h2>Pass Post</h2>
          <button class="close-modal-button" @click="showPassModal = false">
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
        </div>

        <div class="modal-content">
          <div class="form-group">
            <label for="passAmount">Token amount for approval:</label>
            <input
              type="number"
              id="passAmount"
              v-model="passAmount"
              class="form-input"
              min="1"
              placeholder="Enter token amount"
            />
            <p class="form-help">
              Enter the amount of tokens to use for approval
            </p>
          </div>

          <div class="token-balance">
            <p>
              Your current balance: <span>{{ userTokens }}</span> Tokens
            </p>
          </div>
        </div>

        <div class="modal-footer">
          <button class="cancel-button" @click="showPassModal = false">
            Cancel
          </button>
          <button class="submit-button" @click="confirmPass(currentPostIndex)">
            Confirm
          </button>
        </div>
      </div>
    </div>

    <!-- Success Modal -->
    <div
      class="modal-overlay"
      v-if="showSuccessModal"
      @click.self="showSuccessModal = false"
    >
      <div class="modal success-modal">
        <h2>Tokens Staked Successfully</h2>
        <p>You have successfully staked {{ stakeAmount }} tokens.</p>
        <div class="modal-footer centered">
          <button class="submit-button" @click="showSuccessModal = false">
            OK
          </button>
        </div>
      </div>
    </div>

    <!-- Pass Success Modal -->
    <div
      class="modal-overlay"
      v-if="showPassSuccessModal"
      @click.self="showPassSuccessModal = false"
    >
      <div class="modal success-modal">
        <h2>Post Approved Successfully</h2>
        <p>You have approved the post using {{ passAmount }} tokens.</p>
        <div class="modal-footer centered">
          <button class="submit-button" @click="showPassSuccessModal = false">
            OK
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from "vue";
import NavigationBar from "../../components/NavigationBar.vue";
import PostItem from "../../components/ModerationComponents/detailComponent.vue";
import useContentReviewDAO from "../../composables/useContentReviewDAO";
import useContentManager from "../../composables/useContentManager";
import useAuthorManager from "../../composables/useAuthorManager";

// View state
const activeView = ref("dashboard"); // 'dashboard', 'reviewPosts', 'postDetail', 'voting'

// Modal states
const showStakeModal = ref(false);
const showSuccessModal = ref(false);
const showPassModal = ref(false);
const showPassSuccessModal = ref(false);

// User data
const userTokens = ref(1000);
const stakeAmount = ref(100);
const passAmount = ref(10); // Default token amount for passing posts
const pendingPostsCount = ref(3);

// Current post being reviewed
const currentPost = ref({});
const currentPostIndex = ref(null);

const releaseContens = ref([]);
const noreleaseContens = ref([]);
const pendingApplications = ref([]);
const allPendingApplicationDetail = ref([]);

// Voters data
const voters = ref([]);

onMounted(async () => {
  const dao = useContentReviewDAO();
  const author = useAuthorManager();
  const content = useContentManager();

  if (!dao && !content && !author) {
    console.error("Web3 初始化失败");
    return;
  }

  try {
    const account = await author.getAccount();
    if (!account) {
      console.error("未连接钱包");
      return;
    }

    // Fetch content
    const releaseContenIDs = await content.getReleasedArticleIds();
    const noreleaseContenIDS = await content.getUnreleasedArticleIds();
    releaseContens.value = [];
    noreleaseContens.value = [];

    for (let i = 0; i < releaseContenIDs.length; i++) {
      const articleDetail = await content.getArticleDetails(
        releaseContenIDs[i]
      );
      releaseContens.value.push({
        title: "Topic",
        time: "2025-4-12 10:56",
        content: articleDetail[1],
      });
    }

    for (let i = 0; i < noreleaseContenIDS.length; i++) {
      const articleDetail = await content.getArticleDetails(
        noreleaseContenIDS[i]
      );
      noreleaseContens.value.push({
        title: "Topic",
        time: "2025-4-12 10:56",
        content: articleDetail[1],
        id: noreleaseContenIDS[i], // 确保添加 id 字段
      });
    }

    // Update pending posts count
    pendingPostsCount.value = noreleaseContens.value.length;

    // Fetch DAO applications
    const pendingApplicationIDsRaw = await dao.getApplicationQueue();
    const pendingApplicationIDs = [...new Set(pendingApplicationIDsRaw)];
    console.log("Unique application addresses:", pendingApplicationIDs);
    voters.value = []; // 清空已有 voters

    for (let i = 0; i < pendingApplicationIDs.length; i++) {
      const pendingApplicationDetail = await dao.getApplicationDetails(
        pendingApplicationIDs[i]
      );
      const pendingApplicationID = await author.getAuthorIdByAddress(
        pendingApplicationIDs[i]
      );
      voters.value.push({
        address: pendingApplicationIDs[i],
        avatar: "/avatar-placeholder.png",
        id: pendingApplicationID,
        tokens: 10, // 示例 token 数量，根据需要调整
        isApproved: null,
      });
    }
  } catch (error) {
    console.error("获取作者信息失败:", error);
  }
});

// Format post content to properly display markdown-like syntax
const formatPostContent = (content) => {
  if (!content) return "";

  // Replace markdown-like syntax with HTML
  let formattedContent = content
    .replace(/###\s+\*\*(.*?)\*\*/g, '<p class="post-heading">$1</p>')
    .replace(/\*\*(.*?)\*\*/g, "<strong>$1</strong>")
    .replace(/>\s+"(.*?)"/g, '<blockquote>"$1"</blockquote>')
    .replace(/\n\n/g, "</p><p>")
    .replace(/\n/g, "<br>");

  // Wrap in paragraph tags if not already
  if (!formattedContent.startsWith("<p>")) {
    formattedContent = "<p>" + formattedContent;
  }
  if (!formattedContent.endsWith("</p>")) {
    formattedContent = formattedContent + "</p>";
  }

  return formattedContent;
};

// Functions
const stakeTokens = () => {
  if (stakeAmount.value <= 0) {
    alert("Please enter a valid amount");
    return;
  }

  if (stakeAmount.value > userTokens.value) {
    alert("You don't have enough tokens");
    return;
  }

  userTokens.value -= stakeAmount.value;
  showStakeModal.value = false;
  showSuccessModal.value = true;
};

const reviewPost = (post, index) => {
  console.log("Reviewing post:", post);
  currentPost.value = post;
  currentPostIndex.value = index;
  activeView.value = "postDetail";
};

// Modified to show the pass modal instead of immediately processing
const passFunction = async (index) => {
  showPassModal.value = true;
  console.log("我是pass啊");
};

const confirmPass = async (index) => {
  try {
    // 先检查 noreleaseContens 数组里对应的文章是否存在
    const article = noreleaseContens.value[index];
    if (!article || !article.id) {
      throw new Error(`Article at index ${index} is undefined or missing id`);
    }

    // 获取 DAO 实例和账户
    const DAO = useContentReviewDAO();
    const account = await DAO.getAccount();
    if (!account) {
      throw new Error("未连接钱包");
    }

    // 调用 voteForArticleUpdate，传入文章 id 和传递的 token 数量
    await DAO.voteForArticleUpdate(article.id, passAmount.value);
    console.log(
      `Passing post at index: ${index} with ${passAmount.value} tokens`
    );

    // 更新用户 token 数量（示例：扣除对应的 tokens）
    userTokens.value -= passAmount.value;

    // 关闭 pass 模态框
    showPassModal.value = false;

    // 显示成功消息，并从未发布内容中删除已审核的文章
    showPassSuccessModal.value = true;
    noreleaseContens.value.splice(index, 1);
    pendingPostsCount.value = noreleaseContens.value.length;

    // 模拟成功消息显示后返回审核帖子列表
    setTimeout(() => {
      showPassSuccessModal.value = false;
      activeView.value = "reviewPosts";
    }, 2000);
  } catch (error) {
    console.error("Error passing post:", error);
    showPassModal.value = false;
  }
};

const rejectFunction = async (index) => {
  try {
    console.log("Rejecting post at index:", index);
    activeView.value = "reviewPosts";
    noreleaseContens.value.splice(index, 1);
    pendingPostsCount.value = noreleaseContens.value.length;
  } catch (error) {
    console.error("Error rejecting post:", error);
  }
};

const approveVoter = (index) => {
  voters.value[index].isApproved = true;
  const dao = useContentReviewDAO();
  dao.voteForApplicant(voters.value[index].address, true, 10);
};

const rejectVoter = (index) => {
  voters.value[index].isApproved = false;
  const dao = useContentReviewDAO();
  dao.voteForApplicant(voters.value[index].address, false, 10);
};

const visitHomepage = (id) => {
  console.log("Visit homepage of user:", id);
  // Implement navigation to user homepage
};
</script>

<style scoped>
@font-face {
  font-family: "Never";
  src: url("/src/assets/font/Never.otf") format("opentype");
  font-weight: normal;
  font-style: normal;
}

.reviewer-dashboard {
  min-height: 100vh;
  background-color: #0a0118;
  color: white;
  display: flex;
  flex-direction: column;
}

.main-content,
.content-container {
  flex: 1;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  gap: 2rem;
  min-height: calc(100vh - 80px); /* Adjust based on your navbar height */
}

/* Welcome Section */
.welcome-section {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 1rem;
}

.crown-icon {
  width: 60px;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.crown-img {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  object-fit: cover;
}

.welcome-text {
  font-family: "Never", sans-serif;
  font-size: 3rem;
  letter-spacing: 1px;
  margin-top: 1rem;

  /* 渐变文字核心 */
  background: linear-gradient(
    90deg,
    #5e02c7,
    #7902ce,
    #5a26e8,
    #fcf1fe,
    #df01ea
  );
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

/* Action Sections */
.action-section,
.review-section,
.voting-section {
  position: relative;
  border: none; /* 原边框移除，交由伪元素来做 */
  border-radius: 10px;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  transition: all 0.2s ease, transform 0.15s ease;
  z-index: 0;
}

.action-section::before,
.review-section::before,
.voting-section::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 10px; /* 与 voting-section 保持一致 */
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

.stake-button,
.review-button,
.voting-button {
  position: relative;
  background-color: transparent;
  color: white; /* 渐变边框配白色文字更协调 */
  border: none; /* 原边框移除，交给伪元素处理 */
  border-radius: 30px;
  padding: 0.8rem 3rem;
  font-size: 1.2rem;
  cursor: pointer;
  transition: all 0.2s ease, transform 0.15s ease;
  z-index: 0;
}

.stake-button::before,
.review-button::before,
.voting-button::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 30px; /* 保持和按钮一致的圆角 */
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

.stake-button:hover,
.review-button:hover,
.voting-button:hover {
  transform: translateY(-2px) scale(1.02);
  box-shadow: 0 0 20px rgba(223, 1, 234, 0.4);
}

.stake-button:active,
.review-button:active,
.voting-section:active {
  transform: translateY(2px) scale(0.98);
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.5);
}

.review-heading,
.voting-heading {
  font-size: 1.8rem;
  margin-bottom: 2rem;
}

.post-count {
  font-size: 3rem;
  color: #b429ff;
}

/* Back Button */
.back-button {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0;
  margin-bottom: 1rem;
  align-self: flex-start;
}

.back-button svg {
  width: 48px;
  height: 48px;
  color: #b429ff; /* 按钮颜色（紫色） */
  stroke: currentColor; /* 保证SVG跟随color变化 */
  transition: all 0.2s ease;
}

/* 悬浮：发光 + 微放大 */
.back-button:hover svg {
  transform: scale(1.1);
  filter: drop-shadow(0 0 8px rgba(180, 41, 255, 0.6));
}

/* 按下：按压感 + 去掉光影 */
.back-button:active svg {
  transform: scale(0.95) translateY(2px);
  filter: none;
}

/* Posts Container */
.posts-container {
  max-width: 900px;
  margin: 0 auto;
  padding: 1rem;
  flex: 1;
  width: 100%;
  border-radius: 10px;
  border: 1px solid transparent; /* 边框由背景生成，设置为透明 */

  /* 渐变边框 + 内容背景透明 */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;

  /* 玻璃模糊质感 */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

.posts-list {
  max-height: 70vh;
  overflow-y: auto;
  padding-right: 1rem;
}

/* Post Review View */
.post-review-view {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 1rem;
}

.post-detail-wrapper {
  width: 100%;
  max-width: 900px;
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

/* Post Detail Container */
.post-detail-container {
  width: 100%;
  border: 1px solid #b429ff;
  border-radius: 10px;
  overflow: hidden;
  background-color: #110726;
}

.post-detail-content {
  padding: 0;
  overflow-y: auto;
  max-height: 65vh;
}

.post-content-inner {
  padding: 2rem;
  line-height: 1.8;
  font-size: 1.1rem;
}

.post-content-inner p {
  margin-bottom: 1.5rem;
}

.post-content-inner blockquote {
  border-left: 4px solid #b429ff;
  padding-left: 1rem;
  margin-left: 0;
  margin-right: 0;
  font-style: italic;
  color: #e0e0e0;
}

.post-heading {
  font-size: 1.5rem;
  font-weight: bold;
  color: #b429ff;
  margin-bottom: 1.5rem;
}

.sample-content {
  color: #e0e0e0;
}

/* Post Actions */
.post-actions {
  display: flex;
  justify-content: space-between;
  gap: 2rem;
  width: 100%;
  margin-bottom: 2rem;
}

.button-group {
  display: flex;
  gap: 20px;
  align-items: center; /* 保证按钮在容器里垂直居中 */
}

.pass-button {
  width: 200px; /* 宽度 — 统一大小 */
  height: 45px;
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 2rem; /* 左右留白，保持胶囊形 */
  border: none;
  border-radius: 999px;
  font-size: 1.2rem;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 2px;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  position: relative;
  overflow: hidden;
  background-color: transparent;
  z-index: 0;
  box-sizing: border-box;
}

.reject-button {
  width: 200px; /* 宽度 — 统一大小 */
  height: 60px;
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 2rem; /* 左右留白，保持胶囊形 */
  border: none;
  border-radius: 999px;
  font-size: 1.2rem;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 2px;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  position: relative;
  overflow: hidden;
  background-color: transparent;
  z-index: 0;
  box-sizing: border-box;
}

/* 渐变描边统一效果 */
.pass-button::before,
.reject-button::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 999px;
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
  box-sizing: border-box;
}

/* 文字颜色，pass绿色，reject红色 */
.pass-button {
  color: #00ff00;
}

.reject-button {
  color: #ff3b3b;
}

/* hover效果：发光&缩放 */
.pass-button:hover,
.reject-button:hover {
  transform: scale(1.05);
  box-shadow: 0 0 20px rgba(180, 41, 255, 0.6);
}

/* 按下效果：凹陷 */
.pass-button:active,
.reject-button:active {
  transform: scale(0.96);
  box-shadow: inset 0 0 10px rgba(180, 41, 255, 0.7);
}

/* Voting Container */
.voting-container {
  max-width: 900px;
  margin: 0 auto;
  padding: 1rem;
  flex: 1;
  width: 100%;
  border-radius: 10px;
  border: 1px solid transparent; /* 透明边框，配合渐变背景实现边框效果 */

  /* 渐变边框 + 内容区域背景 */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;

  /* 可选：玻璃磨砂质感 */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}
.voting-header {
  display: flex;
  padding: 1rem 0;
  font-weight: bold;
  font-size: 1.2rem;

  /* 移除原单色边框 */
  border-bottom: none;

  /* 渐变下划线效果 */
  position: relative;
}

.voting-header::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: 0;
  height: 2px; /* 下划线厚度可调 */
  width: 100%;
  background: linear-gradient(
    90deg,
    #5e02c7,
    #7902ce,
    #5a26e8,
    #fcf1fe,
    #df01ea
  );
}

.participant-header {
  flex: 1;
}

.staked-header {
  flex: 1;
  text-align: center;
}

.info-header {
  flex: 1;
  text-align: center;
}

.option-header {
  flex: 1;
  text-align: center;
}

.voting-list {
  max-height: 70vh;
  overflow-y: auto;
  padding-right: 1rem;
}

.voter-item {
  display: flex;
  align-items: center;
  padding: 1rem 0;
  border-bottom: 1px solid rgba(180, 41, 255, 0.3);
}

.voter-info {
  flex: 1;
  display: flex;
  align-items: center;
}

.voter-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;
  margin-right: 1rem;
}

.voter-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.voter-id {
  font-weight: bold;
}

.voter-tokens {
  flex: 1;
  text-align: center;
  font-weight: bold;
}

.voter-info-link {
  flex: 1;
  text-align: center;
}

.homepage-button {
  position: relative;
  background-color: transparent;
  color: white; /* 渐变边框搭配白色文字最好看 */
  border: none;
  border-radius: 20px;
  padding: 0.3rem 1rem;
  font-size: 0.8rem;
  cursor: pointer;
  transition: all 0.2s ease, transform 0.1s ease;
  z-index: 0;
}

.homepage-button::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 20px; /* 与按钮本体圆角一致 */
  padding: 2px; /* 渐变边框的厚度 */
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

/* 悬浮时：发光+轻微上浮 */
.homepage-button:hover {
  transform: translateY(-2px) scale(1.02);
  box-shadow: 0 0 20px rgba(223, 1, 234, 0.4);
}

/* 按下时：凹陷按压效果 */
.homepage-button:active {
  transform: translateY(2px) scale(0.98);
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.5);
}

.voter-options {
  flex: 1;
  display: flex;
  justify-content: center;
  gap: 1rem;
}

.approve-button {
  width: auto; /* 自动适应内容，宽度统一 */
  min-width: 100px; /* 最小宽度，视觉上长方形统一 */
  height: 44px; /* 高度固定，形状整齐 */
  padding: 0 1.5rem; /* 内容两边间距统一 */
  border-radius: 999px; /* 胶囊形，长宽自动适应 */
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
  border: none;
  transition: all 0.2s ease, transform 0.1s ease;
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

.reject-button {
  width: auto; /* 自动适应内容，宽度统一 */
  min-width: 100px; /* 最小宽度，视觉上长方形统一 */
  height: 44px; /* 高度固定，形状整齐 */
  padding: 0 1.5rem; /* 内容两边间距统一 */
  border-radius: 999px; /* 胶囊形，长宽自动适应 */
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
  font-weight: bold;
  cursor: pointer;
  border: none;
  transition: all 0.2s ease, transform 0.1s ease;
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

/* 按钮颜色区分，形状完全相同 */
.approve-button {
  background: linear-gradient(
    135deg,
    rgba(0, 255, 157, 0.3),
    rgba(0, 255, 0, 0.4)
  );
  color: #00ff90;
}

.reject-button {
  background: linear-gradient(
    135deg,
    rgba(0, 0, 0, 0.4),
    rgbargba(0, 0, 0, 0.4)
  );
  color: #ff6b6b;
}

/* 悬浮状态：发光 + 微缩放，效果一致 */
.approve-button:hover,
.reject-button:hover,
.approve-button.active,
.reject-button.active {
  transform: translateY(-2px) scale(1.05);
  box-shadow: 0 0 15px rgba(223, 1, 234, 0.4);
}

/* 按下状态：凹陷感，效果一致 */
.approve-button:active,
.reject-button:active {
  transform: translateY(2px) scale(0.95);
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.4);
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

.modal {
  background-color: #0a0118;
  border: 1px solid #b429ff;
  border-radius: 10px;
  width: 90%;
  max-width: 500px;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  border-bottom: 1px solid rgba(180, 41, 255, 0.3);
}

.modal-header h2 {
  margin: 0;
  color: #b429ff;
}

.close-modal-button {
  background: none;
  border: none;
  color: #b429ff;
  cursor: pointer;
  padding: 0;
}

.modal-content {
  padding: 1.5rem;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  padding: 1.5rem;
  border-top: 1px solid rgba(180, 41, 255, 0.3);
}

.modal-footer.centered {
  justify-content: center;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: bold;
}

.form-input {
  width: 100%;
  padding: 0.8rem;
  border: 1px solid rgba(180, 41, 255, 0.3);
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.05);
  color: white;
  font-size: 1rem;
}

.form-help {
  font-size: 0.8rem;
  color: #ccc;
  margin-top: 0.5rem;
}

.token-balance {
  margin-top: 1rem;
  font-size: 0.9rem;
}

.token-balance span {
  font-weight: bold;
  color: #b429ff;
}

.cancel-button,
.submit-button {
  padding: 0.5rem 1.5rem;
  border-radius: 20px;
  cursor: pointer;
  font-size: 1rem;
}

.cancel-button {
  background-color: transparent;
  color: white;
  border: 1px solid white;
}

.submit-button {
  background-color: #b429ff;
  color: white;
  border: none;
}

.success-modal {
  text-align: center;
  padding: 2rem;
}

.success-modal h2 {
  color: #b429ff;
  margin-bottom: 1rem;
}

/* Scrollbar styling */
.posts-list::-webkit-scrollbar,
.voting-list::-webkit-scrollbar,
.post-detail-content::-webkit-scrollbar {
  width: 6px;
}

.posts-list::-webkit-scrollbar-track,
.voting-list::-webkit-scrollbar-track,
.post-detail-content::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 3px;
}

.posts-list::-webkit-scrollbar-thumb,
.voting-list::-webkit-scrollbar-thumb,
.post-detail-content::-webkit-scrollbar-thumb {
  background: #331344;
  border-radius: 3px;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .welcome-section {
    flex-direction: column;
  }

  .welcome-text {
    font-size: 1.8rem;
    text-align: center;
  }

  .review-heading,
  .voting-heading {
    font-size: 1.5rem;
  }

  .voting-header {
    font-size: 0.9rem;
  }

  .voter-item {
    flex-direction: column;
    gap: 0.5rem;
    padding: 1rem;
  }

  .voter-info,
  .voter-tokens,
  .voter-info-link,
  .voter-options {
    width: 100%;
    text-align: left;
  }

  .voter-options {
    justify-content: flex-start;
  }

  .post-actions {
    flex-direction: column;
    gap: 1rem;
  }

  .pass-button,
  .reject-button {
    width: 100%;
  }

  .modal-footer {
    flex-direction: column;
  }

  .cancel-button,
  .submit-button {
    width: 100%;
  }
}
</style>
