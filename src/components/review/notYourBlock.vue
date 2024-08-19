<!-- this is for handling reviewblocks that does not belong to you -->
<!-- <script setup>

</script>



<template>
    <div>
            <div class="likeInfo">
                <button type="button" @click="props.eachComment.score++">
                    <img src="../assets/images/icon-plus.svg" alt="plus-sign">
                </button>
                <p>{{ props.eachComment.score }}</p>
                <button type="button" @click="props.eachComment.score--">
                    <img src="../assets/images/icon-minus.svg" alt="minus-sign">
                </button>
            </div>

            <div class="reviewInfo">
                <div class="reviewPerson">
                    <div class="personInfo">
                        <img :src="getImageSrc(props.eachComment.user.image.png)" alt="personImage">
                        <p class="userName"><span>{{ props.eachComment.user.username }}</span></p>
                        <p class="youTag" v-if="props.currentUser.username === props.eachComment.user.username">you</p>
                        <p class="publishDate"><span>{{ props.eachComment.createdAt }}</span></p>
                    </div>


                    <div class="reviewOwnerFunc" v-if="props.currentUser.username === props.eachComment.user.username">
                        <button class="delete" @click.stop="deleteReview" @updateTotalReviewNum="updateTotalReviewNum">
                            <img src="../assets/images/icon-delete.svg" alt="delete-icon"> Delete
                        </button>
                        <button class="edit"  @click.stop="editReview">
                            <img src="../assets/images/icon-edit.svg" alt="edit-icon"> Edit
                        </button>
                    </div>
                    <button class="reply" @click.stop="addReply" @updateTotalReviewNum="updateTotalReviewNum" v-else>
                        <img src="../assets/images/icon-reply.svg" alt="reply-icon"> Reply
                    </button>
                </div>
                
                <div class="reviewContent">
                    <p class="comment-content">{{ props.eachComment.content }}</p>
                </div>
        </div>
    </div>

</template>


<style scoped>


</style> -->
