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
        totalReviewNum: Number,
        isMobile: Boolean
    })

    const emit = defineEmits(['updateTotalReviewNum','deleteReview','toggleSubList']);
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

    const handleUpdateState = () => {
      replyToggle();
      emit('toggleSubList',true);
    };
    
</script>

<template>
    <div>
        <div class="main-review" v-if="!isMobile">
            <div class="likeInfo">
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
                            <img :src="getImageSrc(props.eachComment.user.image.webp)" alt="personImage">
                            <p class="userName"><span>{{ props.eachComment.user.username }}</span></p>
                            <p class="youTag">you</p>
                            <p class="publishDate"><span>{{ props.eachComment.createdAt }}</span></p>
                        </div>

                        <div class="reviewOwnerFunc">
                            <button class="delete" @click.stop="showDeleteModal = true" v-if="!editReview">
                                <img src="../assets/images/icon-delete.svg" alt="delete-icon"> Delete
                            </button>
                            <button class="edit"  @click.stop="editReview = !editReview">
                                <img src="../assets/images/icon-edit.svg" alt="edit-icon"> Edit
                            </button>

                            <Teleport to="body">
                                <modal :show="showDeleteModal" :purpose="'delete'" @close="showDeleteModal = false" @delete="deleteReview">
                                    <template #header>
                                        <h3>Delete comment</h3>
                                    </template>
                                </modal>
                            </Teleport>
                        </div>
                        
                            
                    </div>
                    
                    <div>
                        <div class="editBlock" v-if="editReview">
                        <textarea @keyup.enter="updateReview" type="text" v-model="updatedContent"></textarea>
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
                </div>

                <!-- when the review does not belong to the current user... -->
                <div class="othersReview" v-else>
                    <div class="reviewPerson">
                        <div class="personInfo">
                            <img :src="getImageSrc(props.eachComment.user.image.webp)" alt="personImage">
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

        <div class="main-review" v-else>
            <div class="reviewInfo">
                <div class="ownReview" v-if="props.currentUser.username === props.eachComment.user.username" >
                    <div class="reviewPerson">
                        <div class="personInfo">
                            <img :src="getImageSrc(props.eachComment.user.image.webp)" alt="personImage">
                            <p class="userName"><span>{{ props.eachComment.user.username }}</span></p>
                            <p class="youTag">you</p>
                            <p class="publishDate"><span>{{ props.eachComment.createdAt }}</span></p>
                        </div>
 
                    </div>
                    <div class="editBlock" v-if="editReview">
                        <textarea @keyup.enter="updateReview" type="text" v-model="updatedContent"></textarea>
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
                            <img :src="getImageSrc(props.eachComment.user.image.webp)" alt="personImage">
                            <p class="userName"><span>{{ props.eachComment.user.username }}</span></p>
                            <p class="publishDate"><span>{{ props.eachComment.createdAt }}</span></p>
                        </div>

                        
                    </div>
                        
                    <div class="reviewContent">
                        <p class="comment-content">{{ props.eachComment.content }}</p>
                    </div>
                    
                </div>
            </div>
            <div class="likeInfo">
                <div class="likeBtn">
                    <button type="button" @click.stop="props.eachComment.score++">
                    <img src="../assets/images/icon-plus.svg" alt="plus-sign">
                    </button>
                    <p>{{ props.eachComment.score }}</p>
                    <button type="button" @click.stop="props.eachComment.score--">
                        <img src="../assets/images/icon-minus.svg" alt="minus-sign">
                    </button>
                </div>

                <!-- dealing with user's own review -->
                <div class="actBtn" v-if="props.currentUser.username === props.eachComment.user.username" >
                    <div class="reviewOwnerFunc">
                            <button class="delete" @click.stop="showDeleteModal = true" v-if="!editReview">
                                <img src="../assets/images/icon-delete.svg" alt="delete-icon"> Delete
                            </button>
                            <button class="edit"  @click.stop="editReview = !editReview">
                                <img src="../assets/images/icon-edit.svg" alt="edit-icon"> Edit
                            </button>

                            <Teleport to="body">
                                <modal :show="showDeleteModal" :purpose="'delete'" @close="showDeleteModal = false" @delete="deleteReview">
                                    <template #header>
                                        <h3>Delete comment</h3>
                                    </template>
                                </modal>
                            </Teleport>
                        </div>
                </div>
                <!-- dealing with other's review -->
                <div class="actBtn" v-else>
                    <button class="reply" @click.stop="replyToggle">
                        <img src="../assets/images/icon-reply.svg" alt="reply-icon"> Reply
                    </button>
                </div>
            </div>
        </div> 

        <replyForm 
                class="replyForm" 
                :reviews="props.eachComment" 
                :currentUser="props.currentUser" 
                :totalReviewNum="props.totalReviewNum"
                :isMobile="isMobile"
                v-if="showReplyForm" 
                @updateState="handleUpdateState"
                @updateTotalReviewNum="updateTotalReviewNum">
        </replyForm >   
    </div>
</template>

<style scoped>

.main-review {
    display: flex;
    flex-direction: row;
    flex: 1;
    /* flex: 0 1 auto; */
    border-radius: 8px;
    padding: .5rem;
}

.reviewContent {
    padding: .5rem;
}

.likeInfo button {
    width: 100%;
    background-color: var(--clr-Vlight-gray);
    border-radius: 10px;
    aspect-ratio: 1/1;
}

.likeInfo {
    background-color: var(--clr-Vlight-gray);
    margin: 1rem 2rem;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    place-items: center;
    gap: 0;
    border-radius: 10px;
    max-height: 8vh;
}

.youTag {
    background-color:var(--clr-moderate-blue);
    color: white;
    font-family: var(--font-family);
    font-size: var(--font-size);
    border-radius: 3px;
    padding: .1rem .5rem;
}

.reviewInfo {
    width: 100%;
    display: flex;
    flex-direction: column;
}

.reviewPerson {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.personInfo {
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: .5rem;
    padding: .5rem;
}

.personInfo publishDate {
    color: var(--clr-light-gray);
}

.personInfo .userName {
    font-family: var(--font-family);
    font-size: var(--font-size);
    font-weight: var(--font-weight-2);
}

.reply,
.edit {
    background-color: transparent;
    color: var(--clr-moderate-blue);
}

.delete {
    background-color: transparent;
    color: var(--clr-soft-red);

}

.editBlock {
    width: 100%;
    display: flex;
    flex-direction: column;
    flex: 1;
    gap: .7rem;
    align-items: end;
    padding: .7rem;
    border-radius: 8px;
    height: fit-content;
 }

.editBlock textarea {
    min-height: 5vh;
    min-width: 100%;
    height: 100%;
    flex: 1;
    padding: 1rem;
    cursor: pointer;
    justify-content: start;
    align-items: start;
    border-color: var(--clr-Vlight-gra);
    box-shadow: 0 0 1px 0;
}

.update {
    background-color: var(--clr-moderate-blue);
    padding: 1.2rem;
    color: white;
}

.replyForm {
    display: flex;
    flex-direction: row;
    gap: .7rem;
    align-items: start;
    padding: 1.5rem;
    border-radius: 8px;
    height: max-content;
}

@media screen and  (max-width: 800px) {
    .main-review {
        flex-direction: column;
        padding: 1rem;
        
    }

    .likeInfo {
        background-color: white;
        flex-direction: row;
        margin: 0;
        padding: 8px;
        height: max-content;
    }

    .likeInfo button {
        aspect-ratio: auto;
        width: auto;
    }

    .likeBtn {
        aspect-ratio: 3/1;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        gap: 0;
        background-color: var(--clr-Vlight-gray);
        border-radius: 5px;
        
    }
    
    .likeBtn p {
        align-self: center;
        justify-content: center;
    }

    .likeBtn button {
        aspect-ratio: 1/1;
        border-radius: 5px;
    }
    
    .reviewOwnerFunc {
        display: flex;
        flex-direction: row;
    }

    .editBlock textarea {
        text-align: left;
        padding: 0.5rem;
    }

    .replyForm {
        flex-direction: column;
        /* width: 100%; */
        height: auto;
        justify-content: center;
        align-items: start;
    }

    .update {
        padding: .5rem 1.2rem;
    }
}

</style>