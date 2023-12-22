<template>
    <div class="input-field-container">
        <div class="input-field">
            <label for="postTitle">Заголовок поста:</label>
            <input type="text" id="postTitle" v-model="postTitle" @click="clearErrorMessage" />
            <br />
            <label for="postContent">Текст поста:</label>
            <textarea id="postContent" v-model="postContent" @click="clearErrorMessage"></textarea>
        </div>
        <button class="btn btn-submit" @click="submitPost">Опубликовать</button>
        <p class="error-message" v-if="errorMassage">{{ errorMassage }}</p>
    </div>
</template>    

<script>
import axios from 'axios';

export default {
    data() {
        return {
            postTitle: '',
            postContent: '',
            errorMassage: '',
        };
    },
    methods: {
        submitPost() {
            if (this.postTitle.trim() === '' || this.postContent.trim() === '') {
                // console.error('Пожалуйста, заполните заголовок и контент поста.');
                this.errorMassage = 'Пожалуйста, заполните заголовок и контент поста.';
                return;
            }

            const postData = {
                title: this.postTitle,
                body: this.postContent,
                userId: this.$root.$data.userId,
            };

            this.$emit('add-post', postData);

            axios.post('https://jsonplaceholder.typicode.com/posts', postData)
                .then(response => {
                    console.log('Пост отправлен на сервер:', response.data);
                })
                .catch(error => {
                    console.error('Ошибка при отправке поста:', error);
                });

            this.clearFields();
        },

        clearFields() {
            this.postTitle = '';
            this.postContent = '';
        },

        clearErrorMessage() {
            this.errorMassage = '';
        }
    }
}
</script>

<style lang="scss" scoped>
.input-field-container {
    padding: 1.2rem;
    border: 1px solid #ccc;
    border-radius: 5px;
    margin-bottom: 2rem;

    .input-field {
        display: flex;
        flex-direction: column;
    }

    & label {
        margin-bottom: 0.4rem;
    }

    & input,
    textarea {
        border: 1px solid #ccc;
        border-radius: 5px;
        padding: 0.4rem;
        margin-bottom: 1rem;
    }

    .error-message {
        color: red;
        margin-top: 0.5rem;
    }
}
</style>