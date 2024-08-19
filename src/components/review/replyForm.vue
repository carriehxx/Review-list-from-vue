<!-- this file aims to handle the submission of reply for for each reviews -->

<script setup>
    import { ref } from 'vue';
    
    const props = defineProps({
        reviews: Object,
        currentUser: Object,
        totalReviewNum: Number
    });

    const emit = defineEmits(['updateTotalReviewNum']);

    const inputMsg = ref('@'+ props.reviews.user.username + ', ' );
    const publishDate = new Date();

    function getImageSrc(imagePaths) {
            const correctedpath = imagePaths.replace("./","/src/assets/");
            return correctedpath;
        }

    // submitting the message and emit back the updated total number of reviews
    function submitMsg(){
        // if condition, when there is an nonempty input being sumitted
        if (inputMsg.value !== ''){
            const newReplyReview = {
                "id": props.totalReviewNum + 1,
                "content": inputMsg.value,
                "createdAt": `${publishDate.getFullYear()}-${publishDate.getMonth() + 1}-${publishDate.getDate()}`,
                "score": 0,
                "replyingTo": props.reviews.user.username,
                "user": props.currentUser,
                "replies": []
            };

            props.reviews.replies.push(newReplyReview);
            // update the total review number
            emit('updateTotalReviewNum', props.totalReviewNum + 1 );
        };
    }

</script>

<template>
    <div>
        <img :src="getImageSrc(props.currentUser.image.png)" alt="currentUserImg">
        <input type="text" class="commentInput" v-model.trim="inputMsg" placeholder="Please write your comment here ...">
        <button @click.stop="submitMsg()" class="reply">REPLY</button>
    </div>
</template>

<style scoped>
.commentInput {
    height: 80%;
    flex: 1;
    /* border-radius: 10px; */
    padding: 1rem;
    cursor: pointer;
}

.commentInput::placeholder {
    /* flex: 1; */
    color: rgb(159, 159, 211);
    /* display: flex; */
    font-size: 1rem;
    line-height: 1.5;
}

.reply {
    background-color: rgb(94, 28, 155);
    padding: 1rem;
    width: 8rem;
    color: azure;
}


</style>