<template>
    <div class="albums">
        <h4>Альбомы:</h4>
        <ul>
            <li v-for="(album, idx) in albums" :key="album.id" :style="{ background: idx % 2 === 0 ? '#fafafa' : '' }">
                <div class="album-item">
                    <h3>{{ idx + 1 }}. {{ album.title }}</h3>
                    <carousel :perPage="5" :perMove="5">
                        <slide v-for="photo in album.photos" :key="photo.id">
                            <img :src="photo.thumbnailUrl" alt="Album Thumbnail" />
                        </slide>
                    </carousel>
                </div>
            </li>
        </ul>
        <br />
        <p>Количество альбомов: {{ albums.length }}</p>
    </div>
</template>
  
<script>
import axios from "axios";
import { Carousel, Slide } from 'vue-carousel';

export default {
    props: ['userId'],

    components: {
        Carousel,
        Slide,
    },

    data() {
        return {
            albums: [],
        };
    },

    created() {
        this.loadUserData();
    },

    methods: {
        loadUserData() {
            axios.get(`https://jsonplaceholder.typicode.com/users/${this.userId}/albums`)
                .then(response => {
                    this.albums = response.data.map(album => {
                        album.photos = [];
                        this.loadAlbumPhotos(album.id);
                        return album;
                    });
                })
                .catch(error => {
                    console.error('Error loading albums data:', error);
                });
        },

        loadAlbumPhotos(albumId) {
            axios.get(`https://jsonplaceholder.typicode.com/photos?albumId=${albumId}`)
                .then(response => {
                    this.albums.find(album => album.id === albumId).photos = response.data;
                })
                .catch(error => {
                    console.error('Error loading album photos:', error);
                });
        },
    },
};
</script>
  
<style lang="scss" scoped>
.album-item {
    margin-bottom: 20px;

    & h3 {
        margin-bottom: 10px;
    }

    & img {
        max-width: 150px;
        height: auto;
    }
}
</style>
  