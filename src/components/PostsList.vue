<template>
    <div class="posts">
        <h4>Посты:</h4>
        <InputField ref="inputField" @add-post="addPost" />
        <ul>
            <li v-for="(post, idx) in posts" :key="post.id + idx" :style="{ background: idx % 2 === 0 ? '#f3f3f3' : '' }">

                <div>
                    <p>{{ idx + 1 }}. <b>Название:</b> {{ post.title }}</p>
                    <button class="btn edit" @click="editTitle(post.id, post)">Изменить</button>
                </div>
                <div>
                    <p><b>Текст:</b> {{ post.body }}</p>
                    <button class="btn edit" @click="editBody(post.id, post)">Изменить</button>
                </div>
            </li>
        </ul>
        <br />
        <p>Количество постов: {{ posts.length }}</p>
    </div>
</template>
  
<script>
import axios from "axios";
import InputField from "@/components/InputField.vue";

export default {
    components: {
        InputField,
    },
    data() {
        return {
            posts: [],
            userId: null,
        };
    },
    created() {
        this.userId = this.$route.params.id;
        this.loadUserData();
    },
    methods: {
        loadUserData() {
            axios.get(`https://jsonplaceholder.typicode.com/users/${this.userId}/posts`)
                .then(response => {
                    this.posts = response.data;
                })
                .catch(error => {
                    console.error('Error loading posts data:', error);
                });
        },

        addPost(postData) {
            axios.post('https://jsonplaceholder.typicode.com/posts', postData)
                .then(response => {
                    if (!this.posts.some(post => post.id === response.data.id)) {
                        this.posts.push(response.data);
                    }
                    this.$refs.inputField.clearFields();
                })
                .catch(error => {
                    console.error('Ошибка при добавлении поста:', error);
                });
        },

        updatePost(postId, data) {
            const apiUrl = `https://jsonplaceholder.typicode.com/posts/${postId}`;
            if (postId === 'new') {
                axios.post(apiUrl, data)
                    .then(response => {
                        this.posts.push(response.data);
                    })
                    .catch(error => {
                        console.error("Error creating post:", error);
                        console.error("Server response:", error.response.data);
                    });
            } else {
                axios.put(apiUrl, data)
                    .then(response => {
                        const postIndex = this.posts.findIndex(p => p.id === postId);

                        if (postIndex !== -1) {
                            this.$set(this.posts, postIndex, response.data);
                        }
                    })
                    .catch(error => {
                        console.error("Error updating post:", error);
                        console.error("Server response:", error.response.data);
                    });
            }
        },

        editTitle(postId, post) {
            const newValue = prompt('Введите новое название', post.title);
            if (newValue !== null) {
                const data = { ...post, title: newValue };
                this.updatePost(postId, data);
            }
        },

        editBody(postId, post) {
            const newValue = prompt('Введите новый текст', post.body);
            if (newValue !== null) {
                const data = { ...post, body: newValue };
                this.updatePost(postId, data);
            }
        },
    },
};
</script>

<style></style>  