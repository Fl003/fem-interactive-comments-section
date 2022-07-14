<template>
  <div id="comment-section">
    <CommentCard 
      v-for="comment in commentdata.comments" :key="comment.id" 
      :comment="comment"
      :currentUser="commentdata.currentUser"
      @update="update"
      @sendUpdate="update"
      @delete="deleteComment"
      @sendDelete="deleteComment"
      @reply="reply"
      @sendReply="reply"/>
  </div>
  <commentForm
    :avatarSrc="userAvatar"
    role="new"
    @new="newComment"/>

  <transition name="fade">
    <div class="background" v-if="showModal"></div>
  </transition>

  <transition name="slide">
    <deleteModal
      v-if="showModal"
      @cancel="cancel"
      @confirm="confirmDelete"/>
  </transition>
</template>

<script>
import data from './data.json'
import CommentCard from './components/comment-card.vue'
import commentForm from './components/comment-form.vue'
import deleteModal from './components/delete-modal.vue'

export default {
  name: 'App',
  data() {
    return {
      commentdata: data,
      userAvatar: data.currentUser.image.png,
      showModal: false,
      deleteId: -1,
    }
  },
  components: {
    CommentCard,
    commentForm,
    deleteModal
  },
  methods: {
    update(value) {
      this.commentdata.comments.forEach(comment => {
        if (comment.id == value[0]) {
          comment.content = value[1];
          return;
        }
        if (comment.replies.length > 0) {
          comment.replies.forEach(reply => {
            if (reply.id == value[0]) {  
              reply.content = value[1];
              return;
            }
          });
        }
      });
    },
    deleteComment(value) {
      this.deleteId = value;
      this.showModal = true;
    },
    confirmDelete() {
      if (this.deleteId == -1) return;
      
      const commentIndex = this.commentdata.comments.findIndex(el => {
        return el.id == this.deleteId;
      })
      if (commentIndex == -1)
      {
        for (let i = 0; i < this.commentdata.comments.length; i++) {
          const repliesIndex = this.commentdata.comments[i].replies.findIndex(el => {
            return el.id == this.deleteId;
          })
          if (repliesIndex != -1) {
            
            this.commentdata.comments[i].replies.splice(repliesIndex, 1);
          }
        }
      } else {
        this.commentdata.comments.splice(commentIndex, 1);
      }

      this.deleteId = -1;
      this.showModal = false;
    },
    cancel() {
      this.deleteId = -1;
      this.showModal = false;
    },
    newComment(value) {
      let comment = {
        "id": this.getNextId(),
        "content": value,
        "createdAt": "today",
        "score": 0,
        "user": {
          "image": {
            "png": this.commentdata.currentUser.image.png,
            "webp": this.commentdata.currentUser.image.webp,
          },
          "username": this.commentdata.currentUser.username
        },
        "replies": []
      }
      this.commentdata.comments.push(comment);
    },
    getNextId() {
      let id = 0;
      this.commentdata.comments.forEach(comment => {
        if (comment.id > id)
          id = comment.id;
        if (comment.replies.length > 0) {
          comment.replies.forEach(reply => {
            if (reply.id > id)
              id = reply.id;
          });
        }
      });

      id++;
      return id;
    },
    reply(value) {
      let newReply = {
        "id": this.getNextId(),
        "content": value[2],
        "createdAt": "today",
        "score": 0,
        "replyingTo": value[1],
        "user": {
          "image": {
            "png": this.commentdata.currentUser.image.png,
            "webp": this.commentdata.currentUser.image.webp,
          },
          "username": this.commentdata.currentUser.username
        }
      }
      
      // this.commentdata.comments[this.commentdata.comments.findIndex(el => {
      //     return el.id == value[0];
      // })].replies.push(newReply);

      this.commentdata.comments.forEach(comment => {
        console.log("id:" + comment.id, value[0]);
        if (comment.id == value[0]) {
          comment.replies.push(newReply);
        } else {
          for (let i = 0; i < comment.replies.length; i++) {
            console.log("reply id:" + comment.replies[i].id, value[0])
            if (comment.replies[i].id == value[0])
              comment.replies.push(newReply);
          }
        }
      });
    }
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap');

:root {
  --primary-blue: hsl(238, 40%, 52%);
  --primary-red: hsl(358, 79%, 66%);
  --primary-light-blue: hsl(239, 57%, 85%);
  --primary-pale-red: hsl(357, 100%, 86%);

  --dark-blue: hsl(212, 24%, 26%);
  --grey-blue: hsl(211, 10%, 45%);
  --light-grey: hsl(223, 19%, 93%);
  --very-light-grey: hsl(228, 33%, 97%);
  --white: hsl(0, 0%, 100%);
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  height: 100%;
}

#app {
  font-family: 'Rubik', sans-serif;
  font-size: 16px;
  min-height: 100%;
  height: auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px 20%;
  background-color: var(--light-grey);
}

.background {
    top: 0;
    left: 0;
    position: fixed;
    width: 100vw;
    height: 100vh;
    background-color: rgba(0, 0, 0, 0.3);
    display: flex;
    align-items: center;
    justify-content: center;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-enter-active,
.slide-leave-active,
.fade-enter-active,
.fade-leave-active {
  transition: all 0.2s linear;
}

.slide-enter-from,
.slide-leave-to {
  transform: translate(-50%, 50%) !important;
  opacity: 0;
}

.slide-enter-to,
.slide-leave-from {
  transform: translateX(-50%, -50%) !important;
  opacity: 1;
}
</style>
