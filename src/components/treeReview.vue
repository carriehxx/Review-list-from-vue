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
    totalReviewNum: Number,
    isMobile: Boolean
});

const emit = defineEmits(['updateTotalReviewNum','idDeleted']);
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
        emit('idDeleted', event); 
        emit('updateTotalReviewNum', props.totalReviewNum - 1); 
    }
}
    
</script>

<!-- ============================================================== -->

<template>
        <div> <!-- each review id is unique -->
            <reviewBlock 
                class="userReviewBlock" 
                :eachComment="props.reviews" 
                :currentUser="props.currentUser" 
                :totalReviewNum="props.totalReviewNum"
                :isMobile="isMobile"
                @click="replyListToggle"
                @updateTotalReviewNum="updateTotalReviewNum"
                @deleteReview="deleteReview"
                @toggleSubList="replyToggles=true">
            </reviewBlock>

            <ul class="reviewContainer" v-show="replyToggles" v-if="containReply(props.reviews)">
                <li v-for="reply in props.reviews.replies" :key="reply.id"> 
                    <treeReview 
                        class="reviewBlocks" 
                        :reviews="reply" 
                        :currentUser="props.currentUser" 
                        :totalReviewNum="props.totalReviewNum"
                        :isMobile="isMobile"
                        @updateTotalReviewNum="updateTotalReviewNum"> 
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
    gap: .5rem;
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
        gap: .5rem;
    }
}

</style>