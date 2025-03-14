<script setup>
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import PrimaryButton from "@/Components/PrimaryButton.vue";
import Artyom from "artyom.js";
import { Head } from "@inertiajs/vue3";
import { onMounted, ref } from "vue";

const artyom = new Artyom();
const text = ref("");

onMounted(() => {
    artyom.initialize({
        lang: "id-ID",
        continuous: true,
        soundex: true,
        debug: true,
    });
});

const sayHello = () => {
    if (text.value.trim() === "") {
        console.warn("Masukkan teks sebelum diucapkan!");
        return;
    }
    artyom.say(text.value, {
        onStart: () => console.log("Speaking..."),
        onEnd: () => console.log("Done speaking."),
    });
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
