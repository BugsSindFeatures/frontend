<template>
  <section>
    <SectionTitle sub :heading="header.heading" :body="header.body" class="!m-0" />

    <div class="progress">
      <div class="bar" :key="newValue" :style="{ width: `${newValue}%` }"></div>
      <button :key="new" @click="calcValue">Click Me!{{ newValue }}</button>
    </div>
  </section>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { useI18n } from "vue-i18n";

export default defineComponent({
  setup() {
    const header = reactive({
      heading: "Tägliche Quests",
      body: "",
    });

    enum value {
      null = 1,
      eins = 20,
      zwei = 40,
      drei = 60,
      vier = 80,
      fuenf = 100,
    }
    var newValue = value.zwei;
    function calcValue(event: any) {
      newValue = value.drei;
      console.log(newValue);
    }
    return { newValue, calcValue, header };
  },
});
</script>

<style scoped>
.progress {
  display: flex;
}
.bar {
  height: 10px;
  background-color: #3f51b5;
  transition: width 2s ease;
}
.barback {
  height: 10px;
  background-color: #3f51b5;
  transition: width 2s ease;
}
</style>
