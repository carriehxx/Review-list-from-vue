<!-- this reviewBlocks aims to consruct a block for every primary 
 review, it assemblies all the necessary components for a review block,
 including the content, the functional buttons and the reply form submission-->

<script setup>
import { ref } from 'vue'
import replyForm from './review/replyForm.vue'
import modal from './review/modal.vue'

    const props = defineProps({
        eachComment: Object,
        currentUser: Object,
        totalReviewNum: Number
    })

    // 传回一个更新的totalReviewNumber
    const emit = defineEmits(['updateTotalReviewNum','deleteReview']);
    // 在父组件中totalReviewNum是ref类型，带一个.value 读取或更新它的值
    function updateTotalReviewNum(newTotal) {
        emit('updateTotalReviewNum', newTotal);
    }

    function getImageSrc(imagePaths) {
        const correctedpath = imagePaths.replace("./","/src/assets/");
        return correctedpath;
    }

    const showReplyForm = ref(false);
    function replyToggle(){
        showReplyForm.value = !showReplyForm.value;
    }

    const showDeleteModal = ref(false);

    function deleteReview(){
        emit('deleteReview', props.eachComment.id)
    }

    const editReview = ref(false);
    const editEmpty = ref(false);
    const updatedContent = ref( props.eachComment.content );
    function updateReview(){
        if (updatedContent.value.trim() !== ''){
            props.eachComment.content = updatedContent.value;
            editReview.value = !editReview.value;
        } else {
            editEmpty.value = true;
            updatedContent.value = props.eachComment.content;
        }
        
    }
    
</script>

<template>
    <div>
        <div class="reviewContainer">
            <div class="likeInfo">
                <!-- 需要考虑一下传回数据 -->
                <button type="button" @click.stop="props.eachComment.score++">
                    <img src="../assets/images/icon-plus.svg" alt="plus-sign">
                </button>
                <p>{{ props.eachComment.score }}</p>
                <button type="button" @click.stop="props.eachComment.score--">
                    <img src="../assets/images/icon-minus.svg" alt="minus-sign">
                </button>
            </div>

            <div class="reviewInfo">
                <div class="ownReview" v-if="props.currentUser.username === props.eachComment.user.username" >
                    <div class="reviewPerson">
                        <div class="personInfo">
                            <img :src="getImageSrc(props.eachComment.user.image.png)" alt="personImage">
                            <p class="userName"><span>{{ props.eachComment.user.username }}</span></p>
                            <p class="youTag">you</p>
                            <p class="publishDate"><span>{{ props.eachComment.createdAt }}</span></p>
                        </div>

                        <div class="reviewOwnerFunc">
                            <button class="delete" @click.stop="showDeleteModal = true">
                                <img src="../assets/images/icon-delete.svg" alt="delete-icon"> Delete
                            </button>
                            <Teleport to="body">
                                <modal :show="showDeleteModal" :purpose="'delete'" @close="showDeleteModal = false" @delete="deleteReview">
                                    <template #header>
                                        <h3>Delete comment</h3>
                                    </template>
                                </modal>
                            </Teleport>
                            <button class="edit"  @click.stop="editReview = !editReview">
                                <img src="../assets/images/icon-edit.svg" alt="edit-icon"> Edit
                            </button>

                        </div>
                            
                    </div>
                    
                    <div class="editBlock" v-if="editReview">
                        <input @keyup.enter="updateReview" type="text" v-model="updatedContent">
                        <button class="update" @click.stop="updateReview">UPDATE</button>
                        <Teleport to="body">
                                <modal :show="editEmpty" :purpose="'edit'" @close="editEmpty = false" @delete="deleteReview">
                                    <template #header>
                                        <h3>Empty content</h3>
                                    </template>
                                </modal>
                            </Teleport>
                    </div>

                    <div class="reviewContent" v-else>
                        <p class="comment-content">{{ props.eachComment.content }}</p>
                    </div>
                </div>

                <!-- when the review does not belong to the current user... -->
                <div class="othersReview" v-else>
                    <div class="reviewPerson">
                        <div class="personInfo">
                            <img :src="getImageSrc(props.eachComment.user.image.png)" alt="personImage">
                            <p class="userName"><span>{{ props.eachComment.user.username }}</span></p>
                            <p class="publishDate"><span>{{ props.eachComment.createdAt }}</span></p>
                        </div>

                        <button class="reply" @click.stop="replyToggle">
                            <img src="../assets/images/icon-reply.svg" alt="reply-icon"> Reply
                        </button>
                    </div>
                        
                    <div class="reviewContent">
                        <p class="comment-content">{{ props.eachComment.content }}</p>
                    </div>
                    
                </div>
            </div>
        </div> 
        <replyForm 
            class="replyForm" 
            :reviews="props.eachComment" 
            :currentUser="props.currentUser" 
            :totalReviewNum="props.totalReviewNum"
            v-if="showReplyForm" 
            @updateState="replyToggle"
            @updateTotalReviewNum="updateTotalReviewNum">
        </replyForm >
    </div>
</template>

<style scoped>

.reviewContainer {
    display: flex;
    flex: 0 1 auto;
    background-color: bisque;
    border-radius: 10px;
}

.reviewContent {
    display: flex;
    flex-direction: row;
    flex: 0 1 auto;
}

.likeInfo {
    padding: 1rem;
    display: flex;
    flex-direction: column;
    gap: 0;
}

.likeInfo button,
.likeInfo p {
    background-color: azure;
}

.reviewInfo {
    display: flex;
    flex-direction: column;
    flex: 1;
    background-color: antiquewhite;
    padding: 1rem;
    gap: 1rem; 
}

.youTag {
    background-color: rgb(58, 58, 109);
    color: azure;
    border-radius: 3px;
    padding: .2rem .5rem;
}

.reviewPerson {
    /* background-color: lightcyan; */
    display: flex;
    justify-content: space-between;
    /* flex: 0,1; */
}

.personInfo {
    display: flex;
    flex-direction: row;
    /* justify-content: center; */
    align-items: center;
    gap: .5rem;
}

.personInfo .userName {
    font-size: 1rem;
    font-weight: bold;;
}

.reply {
    background-color: transparent
}

.comment-content {
    justify-content: start;
    align-items: center;
}

.replyForm {
    display: flex;
    flex: 1;
    gap: 1rem;
    align-items: start;
    justify-content: space-between;
    padding: 1rem;

    border-radius: 10px;
    background-color: blanchedalmond;
}
</style>