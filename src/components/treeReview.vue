<!-- this vue aims to establish the tree structure of the review list -->

<script setup>
    import { ref } from 'vue';
    import reviewBlock from './reviewBlock.vue'
    
    const props = defineProps({
    reviews: {
        type: Array,
        default: () => [] 
    },
    currentUser: Object,
    totalReviewNum: Number
});

    // 传回一个更新的totalReviewNumber
    const emit = defineEmits(['updateTotalReviewNum','idDeleted']);
    // 在父组件中totalReviewNum是ref类型，带一个.value 读取或更新它的值
    function updateTotalReviewNum(newTotal) {
        props.totalReviewNum.value = newTotal;
    }

    const replyToggles = ref({});
    function showReplyToggle(id){
        if (replyToggles.value[id] === undefined) {
            replyToggles.value[id] = true;
        } else {
            replyToggles.value[id] = !replyToggles.value[id];
        }
    }

    function containReply(mainReview) {
        // true if contains replies, else false
        return mainReview.replies && mainReview.replies.length > 0;
    }

    function deleteReview(event) {
        const indexToDelete = props.reviews.findIndex(review => review.id === event);
        props.reviews.splice(indexToDelete,1);
        // emit('idDeleted', event)
        // emit('updateTotalReviewNum', props.totalReviewNum.value - 1)
    }
    
            


</script>

<!-- ============================================================== -->

<template>
    <div>
        <li v-for="mainReview in props.reviews" :key="mainReview.id"> <!-- each review id is unique -->
        <!-- <reviewBlock class="userReviewBlock" @click="showReplyToggle(mainReview.id)" :eachComment="mainReview" :currentUser="props.currentUser" ></reviewBlock> -->
            <reviewBlock 
                class="userReviewBlock" 
                :eachComment="mainReview" 
                :currentUser="props.currentUser" 
                :totalReviewNum="props.totalReviewNum"
                @click="showReplyToggle(mainReview.id)"
                @updateTotalReviewNum="updateTotalReviewNum"
                @deleteReview="deleteReview">
            </reviewBlock>
            <!-- <ul class="reviewContainer" v-show="showReplyStates[mainReview.id]" v-if="containReply(mainReview)"> -->
            <ul class="reviewContainer"  v-show="replyToggles[mainReview.id]" v-if="containReply(mainReview)">
                <treeReview 
                    class="reviewBlocks" 
                    :reviews="mainReview.replies" 
                    :currentUser="props.currentUser" 
                    :totalReviewNum="props.totalReviewNum"
                    @updateTotalReviewNum="updateTotalReviewNum"> </treeReview>
            </ul>
        </li>
    </div>
</template>

<!-- =============================================================== -->

<style scoped>

.userReviewBlock {
    /* width: 80vw; */
    display: flex;
    flex-direction: column;
    /* flex: 0 1 auto; */
    background-color: bisque;
    border-radius: 10px;
}

li {
    list-style: none;
}

</style>