<!-- this file aims to handle the new submission of review for current user -->

<script setup>
import { ref } from "vue";

const props = defineProps({
  reviews: {
    type: Array,
    default: () => [],
  },
  currentUser: Object,
  totalReviewNum: Number,
  isMobile: Boolean,
});

const emit = defineEmits(["updateTotalReviewNum", "updateData"]);

const inputMsg = ref("");

function getImageSrc(imagePaths) {
  return imagePaths;
}

function submitMsg() {
  // console.log("submitting message:", inputMsg.value);
  if (inputMsg.value.trim() !== "") {
    const publishDate = new Date();
    const newReview = {
      id: props.totalReviewNum + 1,
      content: inputMsg.value,
      createdAt: `${publishDate.getFullYear()}-${
        publishDate.getMonth() + 1
      }-${publishDate.getDate()}`,
      score: 0,
      user: props.currentUser,
      replies: [],
    };

    props.reviews.push(newReview);
    emit("updateTotalReviewNum", props.totalReviewNum + 1);
    emit("updateData");
    inputMsg.value = ""; // clear the input field
  }
}
</script>

<template>
  <li v-if="isMobile">
    <textarea
      @keyup.shift.enter="submitMsg"
      type="text"
      class="commentInput"
      v-model.trim="inputMsg"
      placeholder="Add a comment ..."
    ></textarea>
    <div class="bottom">
      <img
        v-if="props.currentUser && props.currentUser.image"
        :src="getImageSrc(props.currentUser.image.webp)"
        alt="currentUserImg"
      />
      <button @click="submitMsg" class="send">SEND</button>
    </div>
  </li>

  <li v-else>
    <img
      v-if="props.currentUser && props.currentUser.image"
      :src="getImageSrc(props.currentUser.image.webp)"
      alt="currentUserImg"
    />
    <textarea
      @keyup.shift.enter="submitMsg"
      type="text"
      class="commentInput"
      v-model.trim="inputMsg"
      placeholder="Add a comment ..."
    ></textarea>
    <button @click="submitMsg" class="send">SEND</button>
  </li>
</template>

<style scoped>
.commentInput {
  min-height: 4vh;
  height: 100%;
  flex: 1;
  padding: 1rem;
  cursor: pointer;
  justify-content: start;
  align-items: start;
  border-color: var(--clr-Vlight-gra);
  box-shadow: 0 0 1px 0;
}

.commentInput::placeholder {
  color: rgb(159, 159, 211);
  font-family: var(--font-family);
  font-size: var(--font-size);
  line-height: 1.5;
  justify-content: start;
  align-items: start;
}

.send {
  background-color: var(--clr-moderate-blue);
  padding: 1.2rem;
  color: white;
}

@media screen and (max-width: 800px) {
  .commentInput {
    text-align: left;
    height: auto;
    width: 100%;
  }

  .send {
    padding: 0.5rem 1.2rem;
  }
}
</style>
