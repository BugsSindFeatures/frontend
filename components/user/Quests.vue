<template>
  <section>
    <!-- +5 MC box -->
    <article
      style="width: fit-content; position: absolute; left: 85%"
      class="flex items-center p-2 bg-warning-light text-warning rounded-md md:rounded-lg"
    >
      <p>+ 5</p>
      <img
        src="/images/coin.png"
        :alt="t('AltAttributes.Morphcoin')"
        class="w-8 h-8 object-contain"
      />
    </article>

    <!-- title -->
    <SectionTitle sub :heading="header.heading" :body="header.body" class="!m-0" />
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

    <!-- quests -->
    <div class="quests">
      <div class="videoQuestWrapper" v-if="videoquest">
        <ul>
          <!-- Videoquest -->
          <div class="flexbox VideoOfVideoquest">
            <!-- <NuxtLink
              :key="i"
              v-for="(course, i) of courses"
              class="flex-shrink-0 snap-center cursor-pointer"
              @click="watchUnseenLecture(course)"
            >
              <CourseCardSm :data="course" />
            </NuxtLink> -->
          </div>
          <li :class="'flexbox videoquest quest' + solvedQuests[0]">
            {{ t("Body.Watch") }} {{ videotitel }}!
          </li>
        </ul>
      </div>
      <hr />

      <!-- Taskquests -->
      <ul class="taskquests">
        <li
          v-for="(quest, index) in taskquests"
          :key="quest"
          :id="'Quest_Nr.' + (index + 1)"
          :class="'taskquest quest' + solvedQuests[index + 1]"
        >
          {{ quest }}
        </li>
      </ul>
    </div>

    <p v-if="lecturesDone === 5">{{ t("Body.SeeYouTomorrow") }}</p>
  </section>
</template>

<script lang="ts">
import { defineComponent, watch } from "vue";
import { useI18n } from "vue-i18n";
import type { Course, GetUnseenLectureResponse, Section } from "~/types/courseTypes";

export default defineComponent({
  setup() {
    const loading = ref(true);
    const { t } = useI18n();
    const router = useRouter();
    const header = reactive({
      heading: "Headings.DailyQuests",
      body: "",
    });

    const myCourses = useMyCourses();

    const courses = computed((): Course[] => {
      return myCourses.value.slice(0, 1);
    });

    var taskquests = [
      "What's special about <div>?",
      "What's special about <ol>?",
      "What's special about <video>?",
      "What's special about <img>?",
    ];
    var lecturesDone = 3;
    var progressbar_progress = 0;
    var videoquest = true;
    var solvedQuests = ["solved", "notsolved", "notsolved", "solved", "solved"];
    var videotitel = "Hacking mit Python";

    if (lecturesDone === 0) {
      progressbar_progress = 1;
    }
    if (lecturesDone > 0) {
      progressbar_progress = lecturesDone * 20;
    }

    onMounted(async () => {
      await getMyCourses();
      loading.value = false;
      if (courses.value && courses.value.length <= 0) {
        Object.assign(header, {
          heading: "EmptyStates.CourseCardSM.Heading",
          body: "EmptyStates.CourseCardSM.Body",
          link: { to: "/profile/courses", text: "Buttons.ViewAll" },
        });
      }
    });
    const watchUnseenLecture = async (course: Course) => {
      const unseenLectureResponse = await getUnseenLecture(course.id);
      console.log(unseenLectureResponse);
      if (unseenLectureResponse)
        router.push({
          path: `/courses/${course.id}/watch`,
          query: {
            section: unseenLectureResponse.section.id,
            lecture: unseenLectureResponse.lecture.id,
          },
        });
    };

    return {
      progressbar_progress,
      header,
      lecturesDone,
      t,
      taskquests,
      videoquest,
      solvedQuests,
      videotitel,
      watchUnseenLecture,
      loading,
      courses,
    };
  },
  //watch()
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
.questsolved {
  color: rgb(47, 225, 86);
}
.questnotsolved::marker {
  color: rgb(60, 184, 159);
  content: "○ - ";
}
.questsolved::marker {
  color: rgb(47, 225, 86);
  content: "● - ";
}
hr {
  color: #3f51b5;
  padding-top: 1%;
  padding-bottom: 1%;
  margin-top: 1%;
}
.videoquest {
  padding-top: 1%;
  padding-bottom: 1%;
  list-style: inside;
}
.taskquest {
  list-style: inside;
}
.VideoOfVideoquest {
  border: 2px solid #3f51b5;
}
.videoQuestWrapper {
  display: flex;
  flex-wrap: nowrap;
  border: 2px solid #3f51b5;
}
</style>
