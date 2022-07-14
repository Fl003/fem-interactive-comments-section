<template>
    <div class="comment-wrapper">
        <div class="comment-card">
            <div class="score-wrapper">
                <img src="../assets/icon-plus.svg" v-on:click="countUp" :class="{ 'active': isGood }">
                <span class="score" :class="{ 'minus': isMinus }">{{score}}</span>
                <img src="../assets/icon-minus.svg" v-on:click="countDown" :class="{ 'active': isBad }">
            </div>
            <div class="content-wrapper">
                <div class="row">
                    <div class="user-wrapper">
                        <img :src="require(`../assets/${this.comment.user.image.png}`)" class="avatar">
                        <p class="username">{{this.comment.user.username}}</p>
                        <span class="me-indicator" v-if="isCurrentUser">you</span>
                        <p class="date">{{this.comment.createdAt}}</p>
                    </div>
                    <div class="options-wrapper">
                        <div class="delete-wrapper" v-if="isCurrentUser" @click="deleteComment">
                            <img src="../assets/icon-delete.svg">
                            <p>Delete</p>
                        </div>
                        <div class="edit-wrapper" v-if="isCurrentUser" @click="editComment">
                            <img src="../assets/icon-edit.svg">
                            <p>Edit</p>
                        </div>
                        <div class="reply-wrapper" v-if="!isCurrentUser" @click="replyToComment">
                            <img src="../assets/icon-reply.svg">
                            <p>Reply</p>
                        </div>
                    </div>
                </div>
                <p class="comment" v-if="!isEditing">{{this.comment.content}}</p>
                <textarea v-model="content" v-if="isEditing"></textarea>
                <button class="update" v-if="isEditing" @click="update">Update</button>
            </div>
        </div>
        <CommentForm
            v-if="isReplying"
            :avatarSrc="this.currentUser.image.png"
            role="reply"
            @reply="reply"/>

        <CommentReplies 
            v-if="hasReplies"
            :replies="this.comment.replies"
            :currentUser="this.currentUser"
            @update="sendUpdate"
            @delete="sendDelete"
            @reply="sendReply"/>
        
    </div>    
</template>

<script>
import CommentReplies from './comment-replies.vue'
import CommentForm from './comment-form.vue'

export default {
    name: "CommentCard",
    data() {
        return {
            score: this.comment.score,
            initialScore: this.comment.score,
            isCurrentUser: this.comment.user.username == this.currentUser.username,
            hasReplies: this.comment.replies.length > 0,
            isEditing: false,
            content: this.comment.content,
            isReplying: false,
            isMinus: false,
            isGood: false, 
            isBad: false,
        }
    },
    props: {
        currentUser: Object,
        comment: Object
    },
    components: {
        CommentReplies,
        CommentForm
    },
    methods: {
        countUp() {
            this.isBad = false;
            this.isMinus = false;
            if (this.score <= this.initialScore) {
                this.score++;
            }
            if (this.score > this.initialScore) {
                this.isGood = true;
            }
        },
        countDown() {
            this.isGood = false;
            this.isMinus = false;
            if (this.score >= this.initialScore) {
                this.score--;
            }
            if (this.score < this.initialScore) {
                this.isBad = true;
            }
            if (this.score < 0) {
                this.isMinus = true;
            }
        },
        deleteComment() {
             this.$emit('delete', this.comment.id);
        },
        editComment() {
            this.$emit('startEditing', this.comment.id);
            this.isEditing = true;
        },
        replyToComment() {
            this.isReplying = !this.isReplying;
        },
        reply(value) {
            this.$emit('reply', [this.comment.id, this.comment.user.username, value]);
            this.isReplying = false;  
        },
        update() {
            this.$emit('update', [this.comment.id, this.content]);
            this.isEditing = false;
        },
        sendUpdate(value) {
            this.$emit('sendUpdate', value);
        },
        sendDelete(value) {
            this.$emit('sendDelete', value);
        },
        sendReply(value) {
            this.$emit('sendReply', value);
        }
    },
    watch: {
        comment: {
            handler(newVal, oldVal) {
                console.log(newVal, oldVal, this.comment.replies.length);
                this.hasReplies = this.comment.replies.length > 0
            },
            deep: true
        },
    }
}
</script>

<style>
    .comment-wrapper {
        width: 100%;
        display: flex;
        flex-direction: column;
        margin: 15px 0px;
    }

    .comment-card {
        width: 100%;
        display: flex;
        background-color: var(--white);
        border-radius: 10px;
        padding: 25px;
    }

    .content-wrapper {
        width: 100%;
        margin-left: 25px;
        display: flex;
        flex-direction: column;
    }

    .content-wrapper textarea {
        width: 100%;
        height: 100px;
        border-radius: 5px;
        border: solid 2px var(--light-grey);
        resize: none;
        padding: 15px 20px;
        font-family: 'Rubik', sans-serif;
        outline: none;
        margin: 20px 0px;
        line-height: 22px;
    }

    .content-wrapper textarea:focus {
        border: solid 2px var(--primary-blue);
    }

    .content-wrapper button {
        color: var(--white);
        font-weight: 700;
        letter-spacing: 1px;
        text-transform: uppercase;
        padding: 15px 30px;
        height: max-content;
        border-radius: 7px;
        border: none;
        width: max-content;
        align-self: flex-end;
        background-color: var(--primary-blue);
        transition: all 0.1s linear;
        cursor: pointer;
    }

    .content-wrapper button:hover {
        background-color: var(--primary-light-blue);
    }

    .content-wrapper .row {
        display: flex;
        justify-content: space-between;
    }

    .user-wrapper,
    .options-wrapper,
    .delete-wrapper,
    .edit-wrapper,
    .reply-wrapper {
        display: flex;
        align-items: center;
    }

    .options-wrapper * {
        margin: 0px 5px;
    }

    .edit-wrapper,
    .reply-wrapper {
        cursor: pointer;
        font-weight: 500;
        filter: invert(33%) sepia(42%) saturate(967%) hue-rotate(201deg) brightness(98%) contrast(90%);
        transition: all 0.1s linear;
    }

    .delete-wrapper {
        font-weight: 500;
        cursor: pointer;
        transition: all 0.1s linear;
        filter: invert(48%) sepia(57%) saturate(545%) hue-rotate(308deg) brightness(95%) contrast(99%);
    }

    .delete-wrapper:hover {
        filter: invert(93%) sepia(49%) saturate(4623%) hue-rotate(289deg) brightness(102%) contrast(113%);
    }

    .edit-wrapper:hover,
    .reply-wrapper:hover {
        filter: invert(77%) sepia(35%) saturate(260%) hue-rotate(201deg) brightness(100%) contrast(88%);
    }

    .edit-wrapper *,
    .reply-wrapper *,
    .delete-wrapper * {
        pointer-events: none;
        user-select: none;
    }

    .user-wrapper .avatar {
        width: 30px;
        height: 30px;
        margin-right: 5px;
    }

    .user-wrapper .username {
        font-weight: 500;
    }

    .user-wrapper .date {
        color: var(--grey-blue);
    }

    .user-wrapper p {
        margin: 0px 5px;
    }

    .me-indicator {
        background-color: var(--primary-blue);
        color: var(--white);
        border-radius: 2px;
        padding: 3px 6px;
        font-size: 13px;
        margin-right: 5px;
    }

    .score-wrapper {
        height: min-content;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        background-color: var(--light-grey);
        border-radius: 10px;
        min-width: 45px;
        max-width: 45px;
        color: var(--primary-blue);
        font-weight: 500;
        transition: all 0.2s linear;
    }

    .score-wrapper .score.minus {
        color: var(--primary-red);
    }

    .score-wrapper img {
        margin: 15px 0px;
        cursor: pointer;
        filter: invert(77%) sepia(35%) saturate(260%) hue-rotate(201deg) brightness(100%) contrast(88%);
        transition: all 0.2s linear;
    }

    .score-wrapper img:hover {
        filter: invert(33%) sepia(42%) saturate(967%) hue-rotate(201deg) brightness(98%) contrast(90%);
    }

    .score-wrapper img.active {
        filter: invert(33%) sepia(42%) saturate(967%) hue-rotate(201deg) brightness(98%) contrast(90%);
    }

    .comment {
        margin-top: 10px;
        color: var(--grey-blue);
        line-height: 22px;
    }
</style>