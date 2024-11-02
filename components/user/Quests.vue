<template>
  <section>
    <!-- +5 MC box -->
    <article
      style="width: fit-content; position: relative; left: 90%; top: 30%"
      :class="`flex items-center p-2 bg-warning-light text-warning rounded-md md:rounded-lg ${grayOutClass}`"
    >
      <p class="boxText">+5</p>
      <img
        src="/images/coin.png"
        :alt="t('AltAttributes.Morphcoin')"
        class="w-8 h-8 object-contain"
      />
    </article>

    <!-- title -->
    <SectionTitle
      sub
      :heading="header.heading"
      :body="header.body"
      class="moveElement15Top !m-0"
    />
    <p class="moveElement15Top" v-if="QuestsDone < 5">
      {{ t("Body.SolveFiveQuests") }}
    </p>
    <p class="moveElement15Top" style="font-size: smaller" v-else-if="QuestsDone === 5">
      {{ t("Body.SolvedFiveQuests") }}
    </p>

    <!-- progress bar -->
    <div class="progress">
      <div
        class="bar progress-bar h-1 w-full rounded items-center"
        :key="progressbar_progress"
        :style="{
          width: `${progressbar_progress}%`,
          backgroundColor: progressbar_color,
        }"
      />
      <!-- Progress pointer box with dynamic position -->
      <div v-if="QuestsDone < 5" class="progress-pointer">{{ QuestsDone }} / 5</div>
    </div>

    <!-- quests -->
    <div v-if="QuestsDone != 5" class="quests">
      <div class="videoQuestWrapper" v-if="videoquest">
        <!-- Videoquest -->
        <div class="flexbox videoquest">
          <IconText
            v-if="solvedQuests[0] === false"
            class="icon mb-2 truncate"
            :icon="VideoIcon.icon"
          >
            {{ t("Body.Watch") }} {{ videotitel }}!
          </IconText>
          <IconText v-else class="greentext icon mb-2 truncate" :icon="VideoIcon.icon">
            {{ t("Body.AlreadyWatched") }} {{ videotitel }}
          </IconText>
        </div>
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
  </section>
</template>

<script lang="ts">
import { defineComponent, watch } from "vue";
import { useI18n } from "vue-i18n";
import type { Course, GetUnseenLectureResponse, Section } from "~/types/courseTypes";
import { FaceFrownIcon, PlayIcon } from "@heroicons/vue/24/outline";

export default defineComponent({
  setup() {
    const loading = ref(true);
    const { t } = useI18n();
    const router = useRouter();
    const header = reactive({
      heading: "Headings.DailyQuests",
      body: "",
    });
    const VideoIcon = computed(() => {
      return {
        icon: PlayIcon,
        text: "",
      };
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
    var progressbar_progress;
    var videoquest = true;
    var progressbar_color = "#3f51b5";
    var solvedQuests = [true, true, true, true, true];
    const QuestsDone = computed((): number => {
      return solvedQuests.filter((quest) => quest === true).length;
    });
    var videotitel = "Hacking mit Python";
    var grayOutClass = "grayed-out";

    if (QuestsDone.value === 0) {
      progressbar_progress = 1;
    }
    if (QuestsDone.value > 0) {
      progressbar_progress = QuestsDone.value * 20;
    }
    if (QuestsDone.value === 5) {
      progressbar_color = "rgb(255, 251, 0)";
    }

    if (QuestsDone.value < 5) {
      var grayOutClass = "";
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
      QuestsDone,
      t,
      taskquests,
      videoquest,
      solvedQuests,
      videotitel,
      watchUnseenLecture,
      loading,
      courses,
      VideoIcon,
      progressbar_color,
      grayOutClass,
    };
  },
});
</script>

<style scoped>
.progress {
  display: flex;
  align-items: center;
  position: relative;
}
.bar {
  height: 10px;
  transition: width 2s ease;
}
.questtrue {
  color: rgb(47, 225, 86);
}
.questfalse::marker {
  color: rgb(60, 184, 159);
  content: "- ";
}
.questtrue::marker {
  color: rgb(47, 225, 86);
  content: "✔ ";
}
hr {
  color: #3f51b5;
  padding-top: 1%;
  padding-bottom: 1%;
  margin-top: 1%;
}
.videoquest {
  padding-top: 2%;
  padding-bottom: 1%;
  list-style: inside;
}
.taskquest {
  list-style: inside;
}
.greentext {
  background-color: rgba(47, 225, 86, 0.25);
}
.boxText {
  margin-right: 10px;
}
/* Box that points to current progress */
.progress-pointer {
  position: relative;
  transform: translateX(-50%); /* Center the box */
  background-color: #3f51b5;
  color: white;
  padding: 2px 8px;
  border-radius: 4px;
  top: -2cqmax;
  text-wrap: nowrap;
}
.progress-pointer::after {
  content: "";
  position: absolute;
  bottom: -6px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-top: 6px solid #3f51b5; /* Match the background color */
}
.grayed-out {
  opacity: 0.5; /* halbtransparent */
  color: gray; /* grauer Text */
  background-color: lightgray; /* optional, falls der Hintergrund auch grau sein soll */
}
.moveElement15Top {
  position: relative;
  top: -15%;
}
</style>
