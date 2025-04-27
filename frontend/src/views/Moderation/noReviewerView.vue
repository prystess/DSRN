<template>
  <div class="moderation-page">
    <!-- Navigation Bar Component -->
    <NavigationBar />

    <!-- Main Content Area -->
    <div class="main-content">
      <div class="restriction-container">
        <!-- Close Icon -->
        <div class="close-icon">
          <img
            src="../../assets/image/close.png"
            alt="Close Icon"
            width="150"
            height="150"
          />
        </div>

        <!-- Apologies Message -->
        <img
          src="../../assets/image/Apologies.png"
          alt="Apologies! Icon"
          width="1000"
          height="230"
        />

        <!-- Divider -->
        <div class="divider"></div>

        <!-- Application Message -->

        <p class="restriction-message">
          Feel free to submit your application here ~
        </p>

        <!-- Apply Button -->
        <button class="apply-button" @click="showApplyModal = true">
          Apply
        </button>
      </div>
    </div>

    <!-- Apply Modal -->
    <div
      class="modal-overlay"
      v-if="showApplyModal"
      @click.self="showApplyModal = false"
    >
      <div class="modal">
        <div class="modal-header">
          <h2>Reviewer Application</h2>
          <button class="close-modal-button" @click="showApplyModal = false">
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
            <label for="reason">Why do you want to be a reviewer?</label>
            <textarea
              id="reason"
              v-model="applicationForm.reason"
              class="form-textarea"
              rows="4"
              placeholder="Please explain why you want to be a reviewer..."
            ></textarea>
          </div>

          <div class="form-group">
            <label for="experience">Relevant experience:</label>
            <textarea
              id="experience"
              v-model="applicationForm.experience"
              class="form-textarea"
              rows="4"
              placeholder="Please describe any relevant experience..."
            ></textarea>
          </div>

          <div class="form-group">
            <label for="tokens">Tokens to stake:</label>
            <p class="form-help">
              Minimum 10 tokens required to become a reviewer
            </p>
          </div>
        </div>

        <div class="modal-footer">
          <button class="submit-button" @click="submitApplication">
            Submit Application
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
        <h2>Application Submitted</h2>
        <p>Your application has been submitted successfully!</p>
        <p>We will review your application and get back to you soon.</p>
        <div class="modal-footer centered">
          <button class="submit-button" @click="showSuccessModal = false">
            OK
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from "vue";
import NavigationBar from "../../components/NavigationBar.vue";
import useContentReviewDAO from "../../composables/useContentReviewDAO";

// Modal state
const showApplyModal = ref(false);
const showSuccessModal = ref(false);

// Application form data
const applicationForm = reactive({
  reason: "",
  experience: "",
  tokens: 100,
});

// Submit application function
const submitApplication = () => {
  const dao = useContentReviewDAO();
  if (!dao) {
    console.error("Web3 初始化失败");
    return;
  }
  const account = dao.getAccount();
  if (!account) {
    console.error("未连接钱包");
    return;
  }
  dao.applyForReviewer();
  dao.applyForReviewer();
};
</script>

<style scoped>
@font-face {
  font-family: "SFProDisplayBlod";
  src: url("/src/assets/font/SFPRODISPLAYBOLD.OTF") format("opentype");
  font-weight: 500;
  font-style: normal;
}
.restriction-message {
  font-family: "SFProDisplayBlod", sans-serif;
  font-size: 1.5rem;
  color: #ba00df;
  letter-spacing: 0.5px;
}

.moderation-page {
  min-height: 100vh;
  background-color: #0a0118;
  color: white;
  display: flex;
  flex-direction: column;
}

.main-content {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem;
}

.restriction-container {
  max-width: 1500px;
  min-height: 600px;
  width: 100%;
  text-align: center;
  padding: 3rem 2rem;
  border-radius: 10px;
  border: 1px solid transparent;

  /* 渐变边框 + 内容背景透明 */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;

  /* 玻璃模糊质感 */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

.close-icon {
  margin-bottom: 2rem;
  color: #b429ff;
}

.close-text {
  font-size: 1.2rem;
  font-weight: bold;
  margin-top: 0.5rem;
}

.restriction-title {
  font-size: 3rem;
  color: #b429ff;
  margin-bottom: 1rem;
  letter-spacing: 2px;
}

.restriction-subtitle {
  font-size: 2rem;
  color: #b429ff;
  margin-bottom: 2rem;
  letter-spacing: 1px;
  line-height: 1.4;
}

.divider {
  height: 1px;
  width: 80%;
  margin: 2rem auto;

  /* 渐变背景线条 */
  background: linear-gradient(
    90deg,
    #5e02c7,
    #7902ce,
    #fcf1fe,
    #5a26e8,
    #df01ea
  );
  border: none;
}

.apply-button {
  position: relative; /* 必须设定，伪元素才能绝对定位 */
  background-color: transparent;
  color: white; /* 字体颜色可配渐变边框风格，建议白色更通用 */
  border: none; /* 不用原生边框，交给 ::before 渐变来做 */
  border-radius: 20px;
  padding: 0.5rem 3rem;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.2s ease;
  z-index: 0;
}

/* 渐变边框，套用tab-button的风格 */
.apply-button::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 20px; /* 保持和按钮本身一致的圆角 */
  padding: 2px; /* 渐变边框厚度 */
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

/* 悬浮：轻微放大+发光阴影 */
.apply-button:hover {
  transform: translateY(-2px) scale(1.02);
  box-shadow: 0 0 20px rgba(223, 1, 234, 0.4);
}

/* 按下：下沉按压效果，内阴影 */
.apply-button:active {
  transform: translateY(2px) scale(0.98);
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.5);
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
  width: 90%;
  max-width: 500px;
  border: 1px solid transparent;
  border-radius: 10px;

  /* 渐变边框 + 透明内容背景 */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;

  /* 玻璃模糊质感 */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
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

.form-textarea,
.form-input {
  width: 100%;
  padding: 0.8rem;
  border: 1px solid rgba(180, 41, 255, 0.3);
  border-radius: 5px;
  background-color: rgba(255, 255, 255, 0.05);
  color: white;
  font-size: 1rem;
  resize: vertical;
  transform: translateX(-13px);
}

.form-help {
  font-size: 0.8rem;
  color: #ccc;
  margin-top: 0.5rem;
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
  position: relative;
  padding: 0.5rem 1.5rem;
  border-radius: 20px;
  cursor: pointer;
  font-size: 1rem;
  background-color: transparent;
  border: none;
  color: white;
  transition: all 0.2s ease, transform 0.15s ease;
  z-index: 0;
}

.submit-button::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 20px; /* 保持和按钮本身一致 */
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

/* 悬浮时：发光 + 微缩放 */
.submit-button:hover {
  transform: translateY(-2px) scale(1.02);
  box-shadow: 0 0 20px rgba(223, 1, 234, 0.4);
}

/* 按下时：下沉，模拟按压感 */
.submit-button:active {
  transform: translateY(2px) scale(0.98);
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.5);
}

.success-modal {
  text-align: center;
  padding: 2rem;
}

.success-modal h2 {
  color: #b429ff;
  margin-bottom: 1rem;
}

.success-modal p {
  margin-bottom: 1rem;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .restriction-title {
    font-size: 2rem;
  }

  .restriction-subtitle {
    font-size: 1.5rem;
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
