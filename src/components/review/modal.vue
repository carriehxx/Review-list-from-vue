<!-- the delete modal -->

<script setup>
    const props = defineProps ({
        show: Boolean,
        purpose: String
        
    })
</script>

<template>

    <transition name="modal">
        <div>
            <div v-if="show && props.purpose === 'delete'" class="modal-mask">
            <div class="modal-container">
                <div class="modal-header">
                    <slot name="header">Delete comment</slot>
                </div>

                <div class="modal-body">
                    <slot name="body">Are you sure you want to delete this comment? This will
                        remove the comment and cant's be undone.
                    </slot>
                </div>

                <div class="modal-footer">
                    <button class="cancel" @click.stop="$emit('close')">
                        NO, CANCEL
                    </button>
                    <button class="deleteReview" @click.stop="$emit(['close','delete'])">
                        YES, DELETE
                    </button>
                </div>
            </div>
        </div>
        <div v-if="show && props.purpose === 'edit'" class="modal-mask">
            <div class="modal-container">
                <div class="modal-header">
                    <slot name="header">Empty content</slot>
                </div>

                <div class="modal-body">
                    <slot name="body">Your comment is empty, cannot be updated. Do you want to continue editting?
                    </slot>
                </div>

                <div class="modal-footer">
                    <button class="cancel" @click.stop="$emit('close')">
                        YES, CONTINUE
                    </button>
                    <button class="deleteReview" @click.stop="$emit(['close','delete'])">
                        NO, DELETE COMMENT
                    </button>
                </div>
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
        transition: opacity 0.3s ease;
    }

    .modal-container {
        width: 350px;
        margin: auto;
        padding: 20px 30px;
        background-color: #fff;
        border-radius: 10px;
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
        transition: all 0.3s ease;
    }

    .modal-header h3 {
        margin-top: 0;
        color: var(--clr-dark-blue);
    }

    .modal-body {
        margin: 20px 0;
        text-align: justify
    }

    .modal-footer {
        display: flex;
        flex: 1;
        gap: 1rem;
        justify-content: center;
    }

    .modal-footer button {
        width: 50%;
    }

    .modal-footer button:hover {
        scale: 1.05;
    }

    .cancel {
        background-color: var( --clr-grayish-blue);
        color: white;
    }

    .deleteReview {
        background-color: var(--clr-soft-red);
        color: white;
    }

    .modal-enter-from {
    opacity: 0;
    }

    .modal-leave-to {
    opacity: 0;
    }

    .modal-enter-from .modal-container,
    .modal-leave-to .modal-container {
    -webkit-transform: scale(1.1);
    transform: scale(1.1);
    }

@media screen and (max-width: 800px) {
    .modal-container {
        width: 80vw;
    }
    .modal-body {
        text-align: left;
    }
}

</style>