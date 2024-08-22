<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import treeReview from './components/treeReview.vue';
import newReview from './components/newReview.vue';
import datastore from './assets/data.json';

const dataStore = ref({
  comments: [],
  currentUser: null,
});
dataStore.value = datastore;

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



function updateTotalReviewNum(newTotal) {
  totalReviewNum.value = newTotal;
}


// checking the screen size to make corresponding layout change
const isMobile = ref(window.innerWidth < 800);

const updateLayout = () => {
  isMobile.value = window.innerWidth < 800;
  };

onMounted(() => {
  totalReviewNum.value = allReviews(dataStore.value.comments)
  window.addEventListener('resize', updateLayout);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', updateLayout);
});

</script>

<template>
  <div class="main-page">
    <ul class="reviewContainer">
      <li class="primary-review" v-for="mainReview in dataStore.comments" :key="mainReview.id">
        <treeReview
          class="reviewBlocks"
          :reviews="mainReview"
          :currentUser="dataStore.currentUser"
          :totalReviewNum="totalReviewNum" 
          :isMobile="isMobile"
          @updateTotalReviewNum="updateTotalReviewNum"
        ></treeReview>
      </li>
      <newReview
        class="sendReview"
        :reviews="dataStore.comments"
        :currentUser="dataStore.currentUser"
        :totalReviewNum="totalReviewNum"
        :isMobile="isMobile"
        @updateTotalReviewNum="updateTotalReviewNum"
      ></newReview>
    </ul>
  </div>
</template>



<style scoped>
.reviewContainer {
  list-style-type: none;
  padding-inline-start: 0%;
  display: flex;
  flex-direction: column;
  flex: 1;
}

.primary-review {
  padding-left: 0;
}

.sendReview {
  display: flex;
  flex-direction: row;
  gap: .7rem;
  align-items: start;
  padding: 1.5rem;
  border-radius: 8px;
  height: max-content;
  margin: 8px 0;
}

@media screen and (max-width: 800px) {
  
  .sendReview {
    display: flex;
    flex-direction: column;
    width: 100%;
    height: auto;
    justify-content: center;
    align-items: start;
  }   
  
  .main-page {
    width: 100%;
  }
}

</style>
