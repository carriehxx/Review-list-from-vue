<!-- the delete modal -->

<script setup>
const props = defineProps({
  show: Boolean,
  purpose: String,
});

const emit = defineEmits(["close", "delete"]);

function handleDelete() {
  let reviews = JSON.parse(localStorage.getItem("reviews")) || [];
  reviews = reviews.filter((review) => review.id !== props.reviewId); // 假设 reviewId 是传入的评论 ID

  localStorage.setItem("reviews", JSON.stringify(reviews));
  emit("delete");
}
</script>

<template>
  <transition name="fade">
    <div v-if="show" class="modal-mask" @click.self="emit('close')">
      <div class="modal-container">
        <div class="modal-header">
          <slot name="header">
            {{ purpose === "delete" ? "Delete comment" : "Empty content" }}
          </slot>
        </div>

        <div class="modal-body">
          <slot name="body">
            {{
              purpose === "delete"
                ? "Are you sure you want to delete this comment? This will remove the comment and cannot be undone."
                : "Your comment is now empty, do you want to continue editing?"
            }}
          </slot>
        </div>

        <div class="modal-footer">
          <button class="cancel" @click.stop="emit('close')">
            {{ purpose === "delete" ? "NO, CANCEL" : "YES, CONTINUE" }}
          </button>
          <button class="deleteReview" @click.stop="handleDelete">
            {{ purpose === "delete" ? "YES, DELETE" : "NO, DELETE IT" }}
          </button>
        </div>
      </div>
    </div>
  </transition>
</template>

<style>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-container {
  width: 350px;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
}

.modal-header h3 {
  margin-top: 0;
  color: var(--clr-dark-blue);
}

.modal-body {
  margin: 20px 0;
  text-align: justify;
}

.modal-footer {
  display: flex;
  gap: 1rem;
  justify-content: center;
}

.modal-footer button {
  flex: 1;
}

.modal-footer button:hover {
  transform: scale(1.05);
}

.cancel {
  background-color: var(--clr-grayish-blue);
  color: white;
}

.deleteReview {
  background-color: var(--clr-soft-red);
  color: white;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}

@media screen and (max-width: 800px) {
  .modal-container {
    width: 90vw;
  }
}
</style>
