<template>
    <div class="container">
        <div class="card user">
            <div class="user-top">
                <h3>{{ title }}</h3>
                <button class="btn" @click="navigateToUsersList()">Назад</button>
            </div>
            <hr />
            <h1>{{ userName }}</h1>
            <div class="btns">
                <button class="btn transition" :class="{ active: contentType === 'user' }"
                    @click="navigateToUserInfo()">Информация</button>
                <button class="btn transition" :class="{ active: contentType === 'albums' }"
                    @click="navigateToAlbums()">Альбомы</button>

                <button class="btn transition" :class="{ active: contentType === 'posts' }"
                    @click="navigateToPosts()">Посты</button>
            </div>
            <div class="content">
                <div class="user-info" v-if="contentType === 'user'">
                    <h4>Личные данные:</h4>
                    <ul>
                        <li>логин: <b>{{ userUsername }}</b></li>
                        <li>email: <b>{{ userEmail }}</b></li>
                        <li>адрес: <b>{{ userStreet }}, {{ userSuite }}, {{ userCity }}, {{ userZipcode }}</b></li>
                    </ul>
                </div>
                <AlbumsList v-if="contentType === 'albums'" :userId="userId"></AlbumsList>
                <PostsList v-if="contentType === 'posts'" :userId="userId"></PostsList>
            </div>
        </div>
    </div>
</template>
  
<script>
import axios from "axios";
import AlbumsList from "@/components/AlbumsList.vue";
import PostsList from "@/components/PostsList.vue";

export default {
    props: ['id'],

    components: {
        AlbumsList,
        PostsList,
    },

    data() {
        return {
            userId: null,
            userName: null,
            userUsername: null,
            userEmail: null,
            userStreet: null,
            userSuite: null,
            userCity: null,
            userZipcode: null,
            contentType: 'user',
            title: 'Пользователь',
        };
    },

    created() {
        this.userId = this.$route.params.id;
        this.contentType = this.$route.query.contentType || 'user';
        this.loadUserData();
    },

    methods: {
        loadUserData() {
            axios.get(`https://jsonplaceholder.typicode.com/users/${this.userId}`)
                .then(response => {
                    this.userName = response.data.name;
                    this.userUsername = response.data.username;
                    this.userEmail = response.data.email;
                    this.userStreet = response.data.address.street;
                    this.userSuite = response.data.address.suite;
                    this.userCity = response.data.address.city;
                    this.userZipcode = response.data.address.zipcode;
                })
                .catch(error => {
                    console.error('Error loading user data:', error);
                });
        },
        navigateToUsersList() {
            this.$router.go(-1)
        },

        navigateToUserInfo() {
            this.contentType = 'user';
            this.updateRoute();
        },
        navigateToAlbums() {
            this.contentType = 'albums';
            this.updateRoute();
        },
        navigateToPosts() {
            this.contentType = 'posts';
            this.updateRoute();
        },
        updateRoute() {
            const newRoute = { name: 'user', params: { id: this.userId }, query: { contentType: this.contentType } };
            if (
                this.$route.name !== newRoute.name ||
                this.$route.params.id !== newRoute.params.id ||
                JSON.stringify(this.$route.query) !== JSON.stringify(newRoute.query)
            ) {
                this.$router.replace(newRoute);
            }
        },
    },
};
</script>
  
<style></style>
  