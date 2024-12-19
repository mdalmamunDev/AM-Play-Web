<!-- src/components/MusicPlayer.vue -->
<template>
    <div class="relative z-10 w-full max-w-md px-4">
        <!-- Album Art -->
        <div class="flex justify-center">
            <img
                    :src="currentSong.cover"
                    alt="Album Cover"
                    class="w-72 h-72 rounded-lg shadow-lg object-cover"
            />
        </div>

        <!-- Song Details -->
        <div class="text-center mt-6">
            <h1 class="text-3xl font-bold">{{ currentSong.title }}</h1>
            <p class="text-gray-400 text-sm mt-2">{{ currentSong.artist }}</p>
        </div>

        <!-- Progress Bar -->
        <div class="mt-6">
            <input type="range" min="0" :max="duration" v-model="currentTime" @input="seekSong"/>
            <div class="flex justify-between text-xs text-gray-400 mt-1">
                <span>{{ formatTime(currentTime) }}</span>
                <span>{{ formatTime(duration) }}</span>
            </div>
        </div>

        <!-- Music Controls -->
        <div class="flex items-center justify-center gap-8 mt-6">
            <button @click="toggleRepeat" :class="{'text-purple-400': isRepeat}">
                <i class="fas fa-repeat"></i>
            </button>
            <button @click="prevSong" class="text-3xl hover:text-purple-400">
                <i class="fa-solid fa-backward"></i>
            </button>
            <button @click="togglePlay" class="bg-purple-500 text-white text-3xl w-16 h-16 rounded-full flex items-center justify-center hover:bg-purple-600 shadow-lg">
                <i v-if="isPlaying" class="fas fa-pause"></i>
                <i v-else class="fas fa-play"></i>
            </button>
            <button @click="nextSong" class="text-3xl hover:text-purple-400">
                <i class="fa-solid fa-forward"></i>
            </button>
            <button @click="toggleShuffle" :class="{'text-purple-400': isShuffle}">
                <i class="fas fa-random"></i>
            </button>
        </div>
    </div>
</template>

<script>
    export default {
        name: "MusicPlayer",
        data() {
            return {
                audioPlayer: new Audio(),
                isPlaying: false,
                isRepeat: false,
                isShuffle: false,
                currentTime: 0,
                duration: 0,
                currentSongIndex: 0,
                playlist: [
                    {
                        title: "Junooniyat",
                        artist: "Akriti Kakar, Shrey Singhal",
                        cover: "https://pagalnew.com/coverimages/Junooniyat-Meet-Bros-500-500.jpg",
                        src: "https://pagalnew.com/128-download/3964",
                    },
                    {
                        title: "Song 2",
                        artist: "Artist 2",
                        cover: "https://via.placeholder.com/400x400",
                        src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3",
                    },
                    {
                        title: "Song 3",
                        artist: "Artist 3",
                        cover: "https://via.placeholder.com/400x400",
                        src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3",
                    },
                ],
            };
        },

        computed: {
            currentSong() {
                return this.playlist[this.currentSongIndex];
            },
        },

        methods: {
            loadSong() {
                this.audioPlayer.src = this.currentSong.src;
                this.audioPlayer.load();
            },

            togglePlay() {
                if (this.isPlaying) {
                    this.audioPlayer.pause();
                } else {
                    this.audioPlayer.play().catch((error) => {
                        console.log("Autoplay failed", error);
                    });
                }
                this.isPlaying = !this.isPlaying;
            },

            nextSong() {
                this.currentSongIndex = this.isShuffle
                    ? Math.floor(Math.random() * this.playlist.length)
                    : (this.currentSongIndex + 1) % this.playlist.length;
                this.updateSong();
            },

            prevSong() {
                this.currentSongIndex =
                    (this.currentSongIndex - 1 + this.playlist.length) % this.playlist.length;
                this.updateSong();
            },

            updateSong() {
                this.isPlaying = false;
                this.loadSong();
            },

            seekSong() {
                this.audioPlayer.currentTime = this.currentTime;
            },

            toggleRepeat() {
                this.isRepeat = !this.isRepeat;
                this.audioPlayer.loop = this.isRepeat;
            },

            toggleShuffle() {
                this.isShuffle = !this.isShuffle;
            },

            formatTime(time) {
                const minutes = Math.floor(time / 60);
                const seconds = Math.floor(time % 60);
                return `${minutes}:${seconds < 10 ? "0" : ""}${seconds}`;
            },
        },

        watch: {
            currentTime(newValue) {
                if (this.audioPlayer.currentTime !== newValue) {
                    this.audioPlayer.currentTime = newValue;
                }
            },
        },

        mounted() {
            this.loadSong();

            this.audioPlayer.addEventListener("loadedmetadata", () => {
                this.duration = this.audioPlayer.duration;
            });

            this.audioPlayer.addEventListener("timeupdate", () => {
                this.currentTime = this.audioPlayer.currentTime;
            });

            this.audioPlayer.addEventListener("ended", () => {
                this.nextSong();
            });
        },
    };
</script>

<style scoped>
    /* Add your component-specific styles here */
</style>
