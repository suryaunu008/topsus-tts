<script setup>
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import { Head } from "@inertiajs/vue3";
import { onMounted, ref } from "vue";

const text = ref("");
const isOn = ref(false);
let recognition;

onMounted(() => {
    // Inisialisasi SpeechRecognition
    const SpeechRecognition =
        window.SpeechRecognition || window.webkitSpeechRecognition;
    recognition = new SpeechRecognition();

    recognition.lang = "id-ID";
    recognition.continuous = true;

    recognition.onstart = function () {
        console.log("Recognition started");
        isOn.value = true;
    };

    recognition.onend = function () {
        console.log("Recognition ended");
        isOn.value = false;
    };

    recognition.onresult = function (event) {
        if (event.results.length > 0) {
            text.value = event.results[event.results.length - 1][0].transcript;
            console.log(text.value);
        }
    };

    recognition.onerror = function (event) {
        console.error("Error occurred in recognition: " + event.error);
    };
});

const mulai = () => {
    recognition.start();
};

const stop = () => {
    recognition.stop();
    text.value = "";
};
</script>

<template>
    <Head title="STT" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                STT
            </h2>
        </template>

        <div class="mx-12 my-8 p-6 bg-white rounded-lg shadow">
            <div class="flex justify-between items-center gap-2">
                <PrimaryButton @click="mulai">Mulai</PrimaryButton>
                <div class="flex flex-col gap-2">
                    <p v-if="isOn" class="text-center text-sm text-gray-500">
                        Sedang mendengarkan 🎤 ...
                    </p>
                    <p v-if="text" class="text-center font-semibold">
                        {{ text }}
                    </p>
                </div>
                <PrimaryButton @click="stop">Stop</PrimaryButton>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
