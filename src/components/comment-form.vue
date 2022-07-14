<template>
    <div class="comment-form">
        <img :src="require(`../assets/${this.avatarSrc}`)">
        <textarea placeholder="Add a comment..." v-model="comment" id="comment-text"></textarea>
        <button id="send-comment" @click="submit">{{buttonText}}</button>
    </div>
</template>

<script>
export default {
    name: "commentForm",
    data() {
        return {
            buttonText: "",
            comment: "",
        }
    },
    props: {
        avatarSrc: String,
        role: String
    },
    mounted() {
        switch (this.role) {
            case "reply":
                this.buttonText = "Reply"
                break;
            default:
                this.buttonText = "Send"
                break;
        }
    },
    methods: {
        submit() {
            if (!this.comment.length > 0) return;

            switch (this.role) {
                case "new":
                    this.$emit('new', this.comment);
                    break;
                case "reply":
                    this.$emit('reply', this.comment);
                    break;
            }
            this.comment = "";
        }
    }
}
</script>

<style scoped>
.comment-form {
    width: 100%;
    display: flex;
    background-color: var(--white);
    border-radius: 10px;
    padding: 25px;
    margin-top: 10px;
}

img {
    width: 45px;
    height: 45px;
}

textarea {
    width: 100%;
    height: 100px;
    border-radius: 5px;
    border: solid 2px var(--light-grey);
    resize: none;
    padding: 15px 20px;
    font-family: 'Rubik', sans-serif;
    outline: none;
    margin: 0px 20px;
}

textarea:focus {
    border: solid 2px var(--primary-blue);
}

button {
    color: var(--white);
    font-weight: 700;
    letter-spacing: 1px;
    text-transform: uppercase;
    padding: 15px 30px;
    height: max-content;
    border-radius: 7px;
    border: none;
    background-color: var(--primary-blue);
    transition: all 0.1s linear;
    cursor: pointer;
}

button:hover {
    background-color: var(--primary-light-blue);
}
</style>