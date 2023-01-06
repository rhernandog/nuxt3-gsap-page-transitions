<script setup>
import { onUnmounted, ref, watch } from "vue";
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

import { useTransitionComposable } from "@/composables/transition-composable";
import transitionConfig from "@/helpers/transitionConfig";

definePageMeta({
  pageTransition: transitionConfig,
});

const { transitionState } = useTransitionComposable();
const main = ref(),
      ctx = ref(),
      scrollTween = ref();

const goToSection = (i) => {
  // Remove the GSAP instance with the specific ID
  // to prevent memory leak
  ctx.value.data.forEach((e) => {
    if (e.vars && e.vars.id === "scrollTween") {
      e.kill();
    }
  });
  ctx.value.add(() => {
    scrollTween.value = gsap.to(window, {
      scrollTo: { y: i * window.innerHeight, autoKill: false },
      duration: 1,
      id: "scrollTween",
      onComplete: () => (scrollTween.value = null),
      overwrite: true,
    });
  });
};

watch(
  () => transitionState.transitionComplete,
  (newValue) => {
    if (newValue) {
      ctx.value = gsap.context((self) => {
        const panels = self.selector(".panel");
        panels.forEach((panel, i) => {
          ScrollTrigger.create({
            trigger: panel,
            start: "top bottom",
            end: "+=200%",
            onToggle: (self) =>
              self.isActive && !scrollTween.value && goToSection(i),
          });
        });
        ScrollTrigger.create({
          start: 0,
          end: "max",
          snap: 1 / (panels.length - 1),
        });
      }, main.value); // <- Scope!
    }
  }
);

onUnmounted(() => {
  ctx.value.revert(); // <- Easy Cleanup!
});

</script>

<template>
  <div ref="main">
    <section class="description panel blue">
      <div>
        <h1>Layered pinning</h1>
        <p>Use pinning to layer panels on top of each other as you scroll.</p>
        <div class="scroll-down">
          Scroll down
          <div class="arrow"></div>
        </div>
      </div>
    </section>
    <section class="panel red">ONE</section>
    <section class="panel orange">TWO</section>
    <section class="panel purple">THREE</section>
    <section class="panel green">FOUR</section>
  </div>
</template>
