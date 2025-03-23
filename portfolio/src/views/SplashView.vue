<script setup>
import { useRouter } from 'vue-router'
import { ref, onMounted } from 'vue'

const router = useRouter()

const audioSrc = new URL('@/assets/midnight-cruiser-capturez-cropped.mp3', import.meta.url).href
// State to track loading progress and if it's done
const loadProgress = ref(0)
const isLoaded = ref(false)
const audioEl = ref(null)
// This will run once on component mount
onMounted(() => {
    // 1. Create an <audio> element for preloading
    audioEl.value = new Audio(audioSrc)
    audioEl.value.loop = true
    audioEl.value.preload = 'auto' // hint to preload

    // 2. Listen for 'progress' to estimate how much is buffered
    audioEl.value.addEventListener('progress', onAudioProgress)

    // 3. Listen for 'canplaythrough' which generally means it can play fully without buffering
    audioEl.value.addEventListener('canplaythrough', onAudioCanPlayThrough, { once: true })
})

function onAudioProgress() {
    if (!audioEl.value) return

    // If the browser has buffered some portion(s) of the audio
    // length > 0 means we have at least one buffered range
    if (audioEl.value.buffered.length > 0) {
        const bufferedEnd = audioEl.value.buffered.end(audioEl.value.buffered.length - 1)
        const duration = audioEl.value.duration || 1 // avoid NaN if duration not available
        const progressFraction = Math.min(bufferedEnd / duration, 1)
        loadProgress.value = progressFraction
    }
}

function onAudioCanPlayThrough() {
    // If canplaythrough fires, it's usually safe to assume the audio is "ready"
    isLoaded.value = true
}

const enterSite = () => {
    if (!audioEl.value) return

    audioEl.value.volume = 0.5
    audioEl.value.play().catch(err => {
        console.warn('Audio autoplay blocked:', err)
    })


    router.push('/home')
}
</script>

<template>
    <div class="splash-view">
        <!-- Fill overlay: expands width from 0% to 100% as loadProgress goes from 0 to 1 -->
        <div class="loading-overlay">
            <div class="loading-fill" :style="{ width: (loadProgress * 100) + '%' }"></div>
        </div>

        <!-- Show "Enter" button only after isLoaded is true -->
        <button v-if="isLoaded" @click="enterSite">
            Enter
        </button>
        <p v-else>Loading audio...</p>
    </div>
</template>

<style scoped>
.splash-view {
    position: fixed;
    inset: 0;
    background-color: #1a1a1a;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    overflow: hidden;
}

/* The container for our "fill" effect. */
.loading-overlay {
    position: absolute;
    inset: 0;
    overflow: hidden;
    z-index: 0;
    /* behind the button/text */
}

/* The actual fill element that expands from left to right */
.loading-fill {
    height: 100%;
    background-color: #000;
    /* or use a brand color, e.g., #22ff22 for a neon fill */
    transition: width 0.5s ease;
}

/* The Enter button */
button {
    position: relative;
    z-index: 1;
    /* above the fill overlay */
    font-size: 2rem;
    padding: 1.5rem 4rem;
    border: 2px solid #fff;
    background: transparent;
    color: #fff;
    border-radius: 4px;
    cursor: pointer;
    text-transform: uppercase;
    letter-spacing: 0.2em;
    transition: all 0.3s ease;
    margin: 1rem;
}

/* Subtle text for "Loading audio..." */
p {
    position: relative;
    z-index: 1;
    color: #fff;
    margin: 1rem;
    font-size: 1.2rem;
}
</style>