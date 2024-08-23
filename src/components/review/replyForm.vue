<!-- this file aims to handle the submission of reply for for each reviews -->

<script setup>
import { ref, onMounted } from "vue";

const props = defineProps({
  reviews: {
    type: Object,
    required: true,
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

const emit = defineEmits(["updateTotalReviewNum", "updateState"]);

const inputMsg = ref("@" + props.reviews.user.username + ", ");

function getImageSrc(imagePaths) {
  return imagePaths;
}

// submitting the message and emit back the updated total number of reviews
function submitMsg() {
  // if condition, when there is an nonempty input being sumitted
  if (inputMsg.value.trim() !== "") {
    const publishDate = new Date();
    const newReplyReview = {
      id: props.totalReviewNum + 1,
      content: inputMsg.value,
      createdAt: `${publishDate.getFullYear()}-${
        publishDate.getMonth() + 1
      }-${publishDate.getDate()}`,
      score: 0,
      replyingTo: props.reviews.user.username,
      user: props.currentUser,
      replies: [],
    };

    props.reviews.replies = [...(props.reviews.replies || []), newReplyReview];
    emit("updateTotalReviewNum", props.totalReviewNum + 1);
    emit("updateState");
    inputMsg.value = "@" + props.reviews.user.username + ", "; // clear the input field
  }
}
</script>

<template>
  <div @click.stop v-if="!isMobile">
    <img
      :src="getImageSrc(props.currentUser.image.webp)"
      alt="currentUserAvatar"
    />
    <textarea
      @keyup.enter.shift="submitMsg"
      type="text"
      class="commentInput"
      v-model.trim="inputMsg"
    >
    </textarea>
    <button @click.stop="submitMsg" class="reply">REPLY</button>
  </div>

  <div @click.stop v-else>
    <textarea
      @keyup.enter.shift="submitMsg"
      type="text"
      class="commentInput"
      v-model.trim="inputMsg"
    >
    </textarea>
    <div class="bottom">
      <img
        :src="getImageSrc(props.currentUser.image.webp)"
        alt="currentUserAvatar"
      />
      <button @click.stop="submitMsg" class="reply">REPLY</button>
    </div>
  </div>
</template>

<style scoped>
.commentInput {
  min-height: 5vh;
  height: 100%;
  flex: 1;
  padding: 1rem;
  cursor: pointer;
  justify-content: start;
  align-items: start;
  border-color: var(--clr-Vlight-gra);
  box-shadow: 0 0 1px 0;
}

.reply {
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
  .reply {
    padding: 0.5rem 1.2rem;
  }
}
</style>
