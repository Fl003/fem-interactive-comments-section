<template>
    <div class="comment-wrapper">
        <div class="comment-card">
            <div class="score-wrapper">
                <img src="../assets/icon-plus.svg" v-on:click="countUp">
                <span class="score">{{score}}</span>
                <img src="../assets/icon-minus.svg" v-on:click="countDown">
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
            isCurrentUser: this.comment.user.username == this.currentUser.username,
            isEditing: false,
            content: this.comment.content,
            isReplying: false,
        }
    },
    props: {
        currentUser: Object,
        comment: Object,
    },
    methods: {
        countUp() {
            this.score++;
        },
        countDown() {
            this.score--;
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