<template>
  <section>
    <SectionTitle sub :heading="header.heading" :body="header.body" class="!m-0" />
    <article
      class="flex items-center p-2 bg-warning-light text-warning rounded-md md:rounded-lg"
    >
      <p>+ 5</p>
      <img
        src="/images/coin.png"
        :alt="t('AltAttributes.Morphcoin')"
        class="w-8 h-8 object-contain"
      />
    </article>
    <p v-if="lecturesDone < 5">
      {{ t("Body.SolveFiveQuests") }}
    </p>
    <p v-else-if="lecturesDone === 5">
      {{ t("Body.SolvedFiveQuests") }}
    </p>

    <!-- progress bar -->
    <div class="progress">
      <div
        class="bar progress-bar h-1 w-full rounded items-center"
        :key="progressbar_progress"
        :style="{ width: `${progressbar_progress}%` }"
      />
    </div>

    <p v-if="lecturesDone === 5">{{ t("Body.SeeYouTomorrow") }}</p>
  </section>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { useI18n } from "vue-i18n";

export default defineComponent({
  setup() {
    const { t } = useI18n();
    const header = reactive({
      heading: "Headings.DailyQuests",
      body: "",
    });

    var lecturesDone = 4;
    var progressbar_progress = 0;

    if (lecturesDone === 0) {
      progressbar_progress = 1;
    }
    if (lecturesDone > 0) {
      progressbar_progress = lecturesDone * 20;
    }

    return { progressbar_progress, header, lecturesDone, t };
  },
  //TODO: change backgroundcolor of progressbar to gold if lecturesDone = 5 using watch!
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
