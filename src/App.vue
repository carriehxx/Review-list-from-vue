<script setup>
import { ref, reactive, onMounted, onBeforeUnmount } from "vue";
import treeReview from "./components/treeReview.vue";
import newReview from "./components/newReview.vue";
import originData from "./assets/data.json";

const dataStore = reactive({});
const totalReviewNum = ref(0);

// checking the screen size to make corresponding layout change
const isMobile = ref(window.innerWidth < 800);

onBeforeUnmount(() => {
  window.removeEventListener("resize", updateLayout);
});

onMounted(() => {
  const dataSaved = localStorage.getItem("data");
  const savedTotalReviewNum = localStorage.getItem("numbers");
  if (dataSaved) {
    //if there is data saved in local storage
    const parsedData = JSON.parse(dataSaved);
    if (parsedData.currentUser && parsedData.comments) {
      Object.assign(dataStore, parsedData);
      // console.log("Data loaded from localStorage:", dataStore);
    } else {
      // console.warn(
      //   "Incomplete data found in localStorage, reloading initial data."
      // );
      Object.assign(dataStore, originData);
    }
  } else {
    // get data from data.json
    Object.assign(dataStore, originData);
  }

  if (savedTotalReviewNum) {
    totalReviewNum.value = JSON.parse(savedTotalReviewNum);
  } else {
    totalReviewNum.value = allReviews(dataStore.comments);
  }

  updateData();
  window.addEventListener("resize", updateLayout);
});

const updateLayout = () => {
  isMobile.value = window.innerWidth < 800;
};

function updateData() {
  localStorage.setItem("data", JSON.stringify(dataStore));
  localStorage.setItem("numbers", JSON.stringify(totalReviewNum.value));
}

function allReviews(dataset) {
  // count all reviews in the dataset
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
  updateData();
}

function deleteReview(event) {
  // console.log("deleteReview", event);
  // console.log("dataStore", dataStore);
  // here the event is the id of the review to be deleted
  const indexToDelete = dataStore.comments.findIndex(
    (review) => review.id === event
  );
  if (indexToDelete !== -1) {
    dataStore.comments.splice(indexToDelete, 1);
    updateData();
  }
}
</script>

<template>
  <div class="main-page">
    <ul class="reviewContainer">
      <li
        class="primary-review"
        v-for="mainReview in dataStore.comments || []"
        :key="mainReview.id"
      >
        <treeReview
          class="reviewBlocks"
          :reviews="mainReview"
          :currentUser="dataStore.currentUser"
          :totalReviewNum="totalReviewNum"
          :isMobile="isMobile"
          @updateTotalReviewNum="updateTotalReviewNum"
          @updateData="updateData"
          @deleteIdxPass="deleteReview"
        ></treeReview>
      </li>
      <newReview
        class="sendReview"
        :reviews="dataStore.comments"
        :currentUser="dataStore.currentUser"
        :totalReviewNum="totalReviewNum"
        :isMobile="isMobile"
        @updateTotalReviewNum="updateTotalReviewNum"
        @updateData="updateData"
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
  gap: 0.7rem;
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
