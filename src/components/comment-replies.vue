<template>
    <div class="comment-replies">
       <div class="wrapper"></div>
       <div class="replies">
            <CommentReply
                v-for="comment in this.replies" :key="comment.id"
                :comment="comment"
                :currentUser="this.currentUser"
                @update="update"
                @delete="deleteComment"
                @reply="reply"/>
       </div>
    </div>
</template>

<script>
import CommentReply from './comment-reply.vue'
import CommentForm from './comment-form.vue'

export default {
    name: "CommentReplies",
    data() {
        return {
        }
    },
    props: {
        replies: Object,
        currentUser: Object
    },
    components: {
        CommentReply,
        CommentForm
    },
    methods: {
        update(value) {
            this.$emit('update', value);
        },
        deleteComment(value) {
            this.$emit('delete', value);
        },
        reply(value) {
            this.$emit('reply', value);
        }
    }
}
</script>

<style>
.comment-replies {
    display: flex;
    margin-top: 10px;
}

.wrapper {
    width: 2px;
    background-color: #DBDBDB;
    margin: 10px 45px;
}

.replies {
    width: 100%;
    display: flex;
    flex-direction: column;
}

.replies div:nth-last-child(1) {
    margin-bottom: 0px;
}
</style>