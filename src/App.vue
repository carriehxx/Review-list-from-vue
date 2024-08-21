<script setup>
import { ref, onMounted} from 'vue';
import treeReview from './components/treeReview.vue';
import newReview from './components/newReview.vue';
import datastore from './assets/data.json';

const dataStore = ref({
  comments: [],
  currentUser: null,
});
dataStore.value = datastore;

// 计算所有评论数量
const totalReviewNum = ref(0);

function allReviews(dataset) {
  if (!dataset || dataset.length === 0) {
    return 0;
  }
  let total = 0;
  for (let i = 0; i < dataset.length; i++) {
    total += 1;
    if (dataset[i].replies && dataset[i].replies.length > 0) {
      total += allReviews(dataset[i].replies);
    }
  }
  return total;
}

onMounted(() => {
  totalReviewNum.value = allReviews(dataStore.value.comments)
});

function updateTotalReviewNum(newTotal) {
  totalReviewNum.value = newTotal;
}
</script>

<template>
  <div class="main-page">
    <ul class="reviewContainer">
      <li class="primary-review" v-for="mainReview in dataStore.comments" :key="mainReview.id">
        <treeReview
          class="replyBlocks"
          :reviews="mainReview"
          :currentUser="dataStore.currentUser"
          :totalReviewNum="totalReviewNum"
          @updateTotalReviewNum="updateTotalReviewNum"
        ></treeReview>
      </li>
      <newReview
        class="sendReview"
        :reviews="dataStore.comments"
        :currentUser="dataStore.currentUser"
        :totalReviewNum="totalReviewNum"
        @updateTotalReviewNum="updateTotalReviewNum"
      ></newReview>
    </ul>
  </div>
</template>

<style scoped>

.main-page {
  padding: 2rem 0rem;
  width: clamp(300px, 60vw, 800px);
  display: flex;
  flex-direction: column;
  /* flex: 1; */
}

.primary-review,
.sendReview {
  list-style: none;
}

.reviewContainer {
  display: flex;
  flex-direction: column;
  flex: 1;
  gap: .5rem;
}

.sendReview {
  display: flex;
  flex: 1;
  gap: .7rem;
  align-items: start;
  justify-content: space-between;
  padding: 1rem;
  border-radius: 7px;
}
</style>
