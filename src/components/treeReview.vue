<!-- this vue aims to establish the tree structure of the review list -->

<script setup>
import { ref } from "vue";
import reviewBlock from "./reviewBlock.vue";

const props = defineProps({
  reviews: {
    type: Object,
    default: () => null,
  },
  currentUser: {
    type: Object,
    required: true,
  },
  totalReviewNum: {
    type: Number,
    required: true,
  },
  isMobile: {
    type: Boolean,
    required: true,
  },
});

const emit = defineEmits([
  "updateTotalReviewNum",
  "updateData",
  "deleteIdxPass",
]);
function updateTotalReviewNum(newTotal) {
  emit("updateTotalReviewNum", newTotal);
}

const isReplyListVisible = ref(true);
function replyListToggle() {
  isReplyListVisible.value = !isReplyListVisible.value;
}

function deleteReview(event) {
  // here the event is the id of the review to be deleted
  // console.log("deleteReview", event);
  // console.log("props.reviews", props.reviews);
  const indexToDelete = props.reviews.replies.findIndex(
    (review) => review.id === event
  );
  if (indexToDelete !== -1) {
    props.reviews.replies.splice(indexToDelete, 1);
    emit("updateTotalReviewNum", props.totalReviewNum - 1);
    emit("updateData");
  }
}

function deleteIdxPass(event) {
  emit("deleteIdxPass", event);
}
</script>

<template>
  <div :key="reviews.id">
    <!-- each review id is unique -->
    <reviewBlock
      class="userReviewBlock"
      :eachComment="props.reviews"
      :currentUser="props.currentUser"
      :totalReviewNum="props.totalReviewNum"
      :isMobile="isMobile"
      @click="replyListToggle"
      @updateTotalReviewNum="updateTotalReviewNum"
      @deleteReview="deleteIdxPass"
      @toggleSubList="isReplyListVisible = true"
      @updateData="emit('updateData')"
    >
    </reviewBlock>

    <ul
      class="reviewContainer"
      v-show="isReplyListVisible"
      v-if="props.reviews.replies && props.reviews.replies.length"
    >
      <li v-for="reply in props.reviews.replies" :key="reply.id">
        <treeReview
          class="reviewBlocks"
          :reviews="reply"
          :currentUser="props.currentUser"
          :totalReviewNum="props.totalReviewNum"
          :isMobile="isMobile"
          @updateTotalReviewNum="updateTotalReviewNum"
          @updateData="emit('updateData')"
          @deleteIdxPass="deleteReview"
        >
        </treeReview>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.userReviewBlock {
  display: flex;
  flex-direction: column;
  border-radius: 10px;
  gap: 0rem;
  width: 100%;
}

li {
  position: relative;
  padding-left: 2rem;
  gap: 0.5rem;
}

li::before {
  content: "";
  position: absolute;
  left: 0;
  top: 0;
  bottom: 0;
  width: 2px;
  background-color: var(--clr-light-gray);
}

@media screen and (max-width: 800px) {
  .userReviewBlock {
    width: 100%;
    gap: 0rem;
  }
  li {
    padding-left: 1rem;
    gap: 0.5rem;
  }
}
</style>
