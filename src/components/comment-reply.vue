<template>
    <div class="comment-wrapper">
        <div class="comment-card">
            <div class="score-wrapper">
                <img src="../assets/icon-plus.svg" alt="plus" v-on:click="countUp" :class="{ 'active': isGood }">
                <span class="score" :class="{ 'minus': isMinus }">{{score}}</span>
                <img src="../assets/icon-minus.svg" alt="minus" v-on:click="countDown" :class="{ 'active': isBad }">
            </div>
            <div class="content-wrapper">
                <div class="row">
                    <div class="user-wrapper">
                        <img :src="require(`../assets/${this.comment.user.image.png}`)" alt="avatar" class="avatar">
                        <p class="username">{{this.comment.user.username}}</p>
                        <span class="me-indicator" v-if="isCurrentUser">you</span>
                        <p class="date">{{this.comment.createdAt}}</p>
                    </div>
                    <div class="options-wrapper">
                        <div class="delete-wrapper" v-if="isCurrentUser" @click="deleteComment">
                            <img src="../assets/icon-delete.svg" alt="delete">
                            <p>Delete</p>
                        </div>
                        <div class="edit-wrapper" v-if="isCurrentUser" @click="editComment">
                            <img src="../assets/icon-edit.svg" alt="edit">
                            <p>Edit</p>
                        </div>
                        <div class="reply-wrapper" v-if="!isCurrentUser" @click="replyToComment">
                            <img src="../assets/icon-reply.svg" alt="reply">
                            <p>Reply</p>
                        </div>
                    </div>
                </div>
                <p class="comment" v-if="!isEditing">
                    <span class="replyTo">@{{this.comment.replyingTo}}</span>
                    {{this.comment.content}}
                </p>
                <textarea v-model="content" v-if="isEditing"></textarea>
                <button class="update" v-if="isEditing" @click="update">Update</button>
            </div>
        </div>
        <CommentForm
            v-if="isReplying"
            :avatarSrc="this.currentUser.image.png"
            role="reply"
            @reply="reply"/>
    </div>    
</template>

<script>
import CommentForm from './comment-form.vue'

export default {
    name: "CommentReply",
    data() {
        return {
            score: this.comment.score,
            initialScore: this.comment.score,
            isCurrentUser: this.comment.user.username == this.currentUser.username,
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
        comment: Object,
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
        }
    },
    components: {
        CommentForm
    }
}
</script>

<style scoped>
.comment .replyTo {
    color: var(--primary-blue);
    font-weight: 500;        
}

.comment-wrapper {
    margin: 0px;
    margin-bottom: 15px;
}
</style>