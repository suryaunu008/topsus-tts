<script setup>
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import { Head } from "@inertiajs/vue3";
import { ref } from "vue";
import Artyom from "artyom.js";

const text = ref("");
const isOn = ref(false);
const artyom = new Artyom();

const startRecognition = () => {
    if (isOn.value) return; // Prevent restarting if already running

    artyom.emptyCommands(); // Clear previous commands to prevent duplication

    // Add a wildcard command to capture any speech
    artyom.addCommands({
        indexes: [".*"], // Capture all speech
        smart: true,
        action: (i, recognizedText) => {
            text.value = recognizedText;
            console.log("Recognized:", recognizedText);
        }
    });

    // Ensure recognized text updates the UI
    artyom.redirectRecognizedTextOutput((recognizedText) => {
        text.value = recognizedText;
    });

    artyom
        .initialize({
            lang: "id-ID",
            continuous: true,
            debug: true,
            listen: true, // Enable recognition
        })
        .then(() => {
            isOn.value = true;
            console.log("Speech recognition started");
        });
};

const stopRecognition = () => {
    if (!isOn.value) return; // Prevent stopping if not running

    artyom.fatality().then(() => {
        isOn.value = false;
        console.log("Speech recognition stopped");
    });
};
</script>

<template>
    <Head title="STT" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                STT with Artyom
            </h2>
        </template>

        <div class="mx-12 my-8 p-6 bg-white rounded-lg shadow">
            <div class="flex justify-between items-center gap-2">
                <PrimaryButton @click="startRecognition">Mulai</PrimaryButton>
                <div class="flex flex-col gap-2">
                    <p v-if="isOn" class="text-center text-sm text-gray-500">
                        Sedang mendengarkan 🎤 ...
                    </p>
                    <p v-if="text" class="text-center font-semibold">
                        {{ text }}
                    </p>
                </div>
                <PrimaryButton @click="stopRecognition">Stop</PrimaryButton>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
