<script setup>
import { onMounted } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
import { ScrollToPlugin } from "gsap/ScrollToPlugin";

import HeaderComponent from "@/components/HeaderComponent.vue";
import { useTransitionComposable } from "./composables/transition-composable";

gsap.registerPlugin(ScrollTrigger, ScrollToPlugin);

const { toggleTransitionComplete } = useTransitionComposable();

onMounted(() => {
  toggleTransitionComplete(true);
});
</script>

<template>
  <HeaderComponent />
  <NuxtPage />
</template>
