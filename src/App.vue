<script setup>
import { ref, onMounted} from 'vue';
import treeReview from './components/treeReview.vue';
import newReview from './components/newReview.vue';
import reviewData from './assets/data.json';

const dataStore = ref(reviewData);

// 计算所有评论数量
const totalReviewNum = ref(0);
function allReviews(dataset){
  if (dataset.length === 0){
    return 0;
  }
  let total = 0;
  
  for (let i = 0; i < dataset.length; i++) {
    total += 1; // 当前评论本身计入总数
    total += allReviews(dataset[i].replies ? dataset[i].replies : []); // 递归计算子评论的数量
  }

  totalReviewNum.value = total;
  return total;
}

// 组件挂载时候计算
onMounted(() => {
  totalReviewNum.value = allReviews(dataStore.value.comments);
});

function updateTotalReviewNum(newTotal) {
    totalReviewNum.value = newTotal;
}

// function updateIndexing(dataset, referenceId) {
//   if (dataset.length === 0) return;

//   for (let i = 0; i < dataset.length; i++) {
//     // 更新当前评论的 ID
//     if (dataset[i].id > referenceId) {
//       dataset[i].id -= 1;
//     }

//     // 如果有子评论，递归调用
//     if (dataset[i].replies) {
//       updateIndexing(dataset[i].replies, referenceId);
//     }
//   }
// }

</script>


<template>
  <!-- Reviewlists -->
  <div class="main-page" >
    <ul class="reviewContainer">
      <treeReview 
          class="reviewBlocks" 
          :reviews="dataStore.comments" 
          :currentUser="dataStore.currentUser" 
          :totalReviewNum="totalReviewNum"
          @updateTotalReviewNum="updateTotalReviewNum"></treeReview>
      <newReview class="sendReview" 
        :reviews="dataStore.comments" 
          :currentUser="dataStore.currentUser" 
          :totalReviewNum="totalReviewNum"
          @updateTotalReviewNum="updateTotalReviewNum"></newReview>
    </ul>
  </div>
</template>

<style scoped>
.main-page {
  padding: 3vw 3vh;
  /* background-color: rgb(108, 236, 236); */
}

.reviewBlocks {
  text-decoration: none;
  cursor: pointer;
  border-radius: 10px;
  /* background-color: blanchedalmond; */
}

.sendReview {
  display: flex;
  flex: 1;
  gap: 1rem;
  align-items: start;
  justify-content: space-between;
  padding: 1rem;

  border-radius: 10px;
  background-color: blanchedalmond;
}

/* 
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
} */


</style>
