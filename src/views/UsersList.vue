<template>
    <div class="container">
        <div class="card user">
            <h1>{{ title }}</h1>
            <hr />
            <ul v-if="users.length !== 0" class="list">
                <li class="user-list" v-for="(user, idx) in     users    " :key="user.id"
                    :style="{ background: idx % 2 === 0 ? '#f3f3f3' : '' }">
                    {{ idx + 1 }}. {{ user.name }}

                    <div class="btns">
                        <button class="btn open" @click="navigateToUserPage(user.id, 'user')">Инфо</button>
                        <button class="btn open" @click="navigateToUserPage(user.id, 'albums')">Альбомы</button>
                        <button class="btn open open-last" @click="navigateToUserPage(user.id, 'posts')">Посты</button>
                    </div>
                </li>
                <br />
                <li>Общее количество пользователей: {{ users.length }}</li>
            </ul>
            <div v-else>Пользователей пока нет. Добавьте первого</div>
        </div>
    </div>
</template>
  
<script>
import axios from "axios";

export default {
    data() {
        return {
            title: 'Список пользователей',
            users: [],
        }
    },
    created() {
        this.loadUsers();
    },
    methods: {
        loadUsers() {
            axios.get('https://jsonplaceholder.typicode.com/users')
                .then(response => {
                    this.users = response.data;
                })
                .catch(error => {
                    console.error('Error loading user data:', error);
                });
        },
        navigateToUserPage(userId, contentType) {
            this.$router.push({ name: 'user', params: { id: userId }, query: { contentType: contentType } });
        },
    },
}
</script>
  
<style></style>
  