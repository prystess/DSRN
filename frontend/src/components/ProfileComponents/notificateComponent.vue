<template>
  <div class="notification-item">
    <div class="notification-header">
      <div class="notification-icon" :class="type">
        <!-- Comment Icon -->
        <img
          v-if="type === 'comment'"
          src="../../assets/image/comment.png"
          alt="Comment Icon"
          width="30"
          height="30"
        />
        <!-- Like Icon -->
        <img
          v-if="type === 'like'"
          src="../../assets/image/Like.png"
          alt="Like Icon"
          width="30"
          height="30"
        />

        <!-- Collect Icon -->
        <img
          v-if="type === 'collect'"
          src="../../assets/image/Collect.png"
          alt="Collect Icon"
          width="30"
          height="30"
        />

        <!-- Reward Icon -->
        <img
          v-if="type === 'reward'"
          src="../../assets/image/Reward.png"
          alt="Reward Icon"
          width="30"
          height="30"
        />
      </div>

      <div class="notification-avatar">
        <img :src="avatar" alt="User Avatar" />
      </div>
    </div>

    <div class="notification-content">
      <div class="notification-text">{{ content }}</div>
      <div v-if="comment" class="notification-comment">{{ comment }}</div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  type: {
    type: String,
    required: true,
    validator: (value) =>
      ["comment", "like", "collect", "reward"].includes(value),
  },
  avatar: {
    type: String,
    required: true,
  },
  content: {
    type: String,
    required: true,
  },
  comment: {
    type: String,
    default: "",
  },
});
</script>

<style scoped>
.notification-item {
  display: flex;
  flex-direction: column;
  padding: 1.5rem; /* �����ڱ߾� */
  border-radius: 10px;
  border: 1px solid transparent; /* ����͸���߿�ʹ�ý���߿� */

  /* ����߿� */
  background: linear-gradient(#0a0118, #0a0118) padding-box,
    linear-gradient(90deg, #5e02c7, #7902ce, #5a26e8, #fcf1fe, #df01ea)
      border-box;

  /* ����ģ��Ч�� */
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);

  margin-bottom: 1rem;
}

.notification-header {
  display: flex;
  align-items: center;
  margin-bottom: 0.75rem;
}

.notification-icon {
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 0.75rem;
  color: #b429ff;
}

.notification-icon.like,
.notification-icon.collect {
  color: #b429ff;
}

.notification-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;
}

.notification-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.notification-content {
  margin-left: 0.5rem;
}

.notification-text {
  margin-bottom: 0.5rem;
}

.notification-comment {
  color: #ccc;
  line-height: 1.4;
  padding: 0.5rem;
  background-color: rgba(255, 255, 255, 0.05);
  border-radius: 5px;
}
</style>
