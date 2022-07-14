<template>
    <div class="modal">
        <h1>Delete comment</h1>
        <p>
            Are you sure you want to delete this comment? This will remove the comment and can't be undone.
        </p>
        <div class="row">
            <button id="cancel" @click="cancelModal">No, cancel</button>
            <button id="confirm" @click="deleteComment">Yes, delete</button>
        </div>
    </div>
</template>

<script>
export default {
    name: "deleteModal",
    data() {
        return {

        }
    },
    methods: {
        cancelModal() {
            this.$emit('cancel');
        },
        deleteComment() {
            this.$emit('confirm');
        }
    }
}
</script>

<style scoped>

.modal {
    border-radius: 5px;
    background-color: var(--white);
    width: 375px;
    padding: 25px;
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

h1 {
    font-size: 25px;
    color: var(--dark-blue);
}

p {
    color: var(--grey-blue);
    margin: 15px 0px;
    line-height: 22px;
}

.row {
    display: flex;
    justify-content: space-between;
}

button {
    width: 155px;
    height: 40px;
    border: none;
    text-transform: uppercase;
    color: var(--white);
    font-weight: 600;
    letter-spacing: 1px;
    border-radius: 5px;
    cursor: pointer;
    transition: all 0.1s linear;
}

#cancel {
    background-color: var(--grey-blue);
}

#cancel:hover {
    background-color: var(--dark-blue);
}

#confirm {
    background-color: var(--primary-red);
}

#confirm:hover {
    background-color: var(--primary-pale-red);
}
</style>