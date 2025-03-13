<script setup>
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import Artyom from "artyom.js";
import { Head } from "@inertiajs/vue3";
import { onMounted, ref } from "vue";

const artyom = new Artyom();
const text = ref("");
let audioContext,
    mediaRecorder,
    audioChunks = [];

onMounted(() => {
    artyom.initialize({
        lang: "id-ID",
        continuous: true,
        soundex: true,
        debug: true,
    });

    audioContext = new (window.AudioContext || window.webkitAudioContext)();
});

const startRecording = () => {
    const destination = audioContext.createMediaStreamDestination();
    mediaRecorder = new MediaRecorder(destination.stream);
    audioChunks = [];

    mediaRecorder.ondataavailable = (event) => {
        audioChunks.push(event.data);
    };

    mediaRecorder.onstop = () => {
        const audioBlob = new Blob(audioChunks, { type: "audio/wav" });
        const audioUrl = URL.createObjectURL(audioBlob);

        const downloadLink = document.createElement("a");
        downloadLink.href = audioUrl;
        downloadLink.download = "artyom-voice.wav";
        document.body.appendChild(downloadLink);
        downloadLink.click();
        document.body.removeChild(downloadLink);
    };

    mediaRecorder.start();

    return destination;
};

const sayHello = () => {
    if (!text.value) return;

    const destination = startRecording();
    const utterance = new SpeechSynthesisUtterance(text.value);

    utterance.onstart = () => {
        console.log("Rekaman dimulai...");
    };

    utterance.onend = () => {
        console.log("Rekaman selesai, menyiapkan unduhan...");
        mediaRecorder.stop();
    };

    // Menghubungkan sumber audio ke audio context
    const source = audioContext.createMediaStreamSource(destination.stream);
    source.connect(audioContext.destination);

    // Menghubungkan utterance ke audio context
    const utteranceSource = audioContext.createMediaStreamSource(
        destination.stream
    );
    utteranceSource.connect(audioContext.destination);

    window.speechSynthesis.speak(utterance);
};
</script>

<template>
    <Head title="TTS" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                TTS
            </h2>
        </template>

        <div
            class="mx-12 my-8 p-6 bg-white rounded-lg shadow flex justify-center items-center gap-2"
        >
            <input
                v-model="text"
                type="text"
                class="w-1/2 p-2 border border-gray-300 rounded-lg"
                placeholder="Teks yang akan diucapkan"
                @keyup.enter="sayHello"
            />
            <PrimaryButton @click="sayHello">Ucapkan</PrimaryButton>
        </div>
    </AuthenticatedLayout>
</template>
