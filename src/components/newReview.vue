<!-- this file aims to handle the new submission of review for current user -->
<!-- 基本可以不用动了，现在这个可以更新reviews的dataset以及total review number -->

<script setup>
import { ref } from 'vue'

const props = defineProps({
    reviews: {
        type: Array,
        default: () => [] 
    },
    currentUser: Object,
    totalReviewNum: Number
})

const emit = defineEmits(['updateTotalReviewNum']);

const inputMsg = ref('');
const publishDate = new Date();

function getImageSrc(imagePaths) {
        const correctedpath = imagePaths.replace("./","/src/assets/");
        return correctedpath;
    }

function submitMsg(){
    // if condiition, when there is an nonempty input being sumitted

    if (inputMsg.value.trim() !== ''){
        const newReview = {
            "id": props.totalReviewNum + 1,
            "content": inputMsg.value,
            "createdAt": `${publishDate.getFullYear()}-${publishDate.getMonth() + 1}-${publishDate.getDate()}`,
            "score": 0,
            "user": props.currentUser,
            "replies": []
        };

        props.reviews.push(newReview);
        // update the total review number
        emit('updateTotalReviewNum', props.totalReviewNum + 1);
        inputMsg.value = ''; //恢复
    };
}

</script>

<template>
    <li>
        <img :src="getImageSrc(props.currentUser.image.png)" alt="currentUserImg">
        <input @keyup.enter="submitMsg" type="text" class="commentInput" v-model.trim="inputMsg" placeholder="Add a comment ...">
        <button  @click="submitMsg" class="send">SEND</button>
    </li>
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

.send {
    background-color: rgb(94, 28, 155);
    padding: 1rem;
    width: 8rem;
    color: azure;
}


</style>