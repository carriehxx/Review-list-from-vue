<!-- this vue aims to establish the tree structure of the review list -->

<script setup>
import { ref } from 'vue';
import reviewBlock from './reviewBlock.vue'

const props = defineProps({
    reviews: {
        type: Object,
        default: () => {} 
    },
    currentUser: Object,
    totalReviewNum: Number
});

    // 传回一个更新的totalReviewNumber
const emit = defineEmits(['updateTotalReviewNum','idDeleted']);
// 在父组件中totalReviewNum是ref类型，带一个.value 读取或更新它的值
function updateTotalReviewNum(newTotal) {
    emit('updateTotalReviewNum', newTotal);
}

const replyToggles = ref(true);
function replyListToggle(){
    replyToggles.value = !replyToggles.value;
}

function containReply(mainReview) {
    // true if contains replies, else false
    return mainReview.replies && mainReview.replies.length > 0;
}

function deleteReview(event) {
    const indexToDelete = props.reviews.findIndex(review => review.id === event);
    if (indexToDelete !== -1) {
        props.reviews.splice(indexToDelete, 1);
        emit('idDeleted', event);  // 通知父组件评论已被删除
        emit('updateTotalReviewNum', props.totalReviewNum - 1);  // 更新评论总数
    }
}
    
</script>

<!-- ============================================================== -->

<template>
        <li> <!-- each review id is unique -->
            <reviewBlock 
                class="userReviewBlock" 
                :eachComment="props.reviews" 
                :currentUser="props.currentUser" 
                :totalReviewNum="props.totalReviewNum"
                @click="replyListToggle"
                @updateTotalReviewNum="updateTotalReviewNum"
                @deleteReview="deleteReview">
            </reviewBlock>

            <ul class="reviewContainer" v-show="replyToggles" v-if="containReply(props.reviews)">
                <li v-for="reply in props.reviews.replies" :key="reply.id"> 
                    <treeReview 
                        class="reviewBlocks" 
                        :reviews="reply" 
                        :currentUser="props.currentUser" 
                        :totalReviewNum="props.totalReviewNum"
                        @updateTotalReviewNum="updateTotalReviewNum"> 
                    </treeReview>
                </li>
            </ul>
        </li>
</template>

<!-- =============================================================== -->

<style scoped>

.userReviewBlock {
    /* width: 80vw; */
    display: flex;
    flex-direction: column;
    /* flex: 0 1 auto; */
    border-radius: 10px;
}

li {
    position: relative;
    gap: .5rem;
}

/* li::before {
    content: "";
    position: absolute;
    left: .7rem;
    width: 1px;
    background-color: hsl(--clr-grayish-blue);

} */

</style>