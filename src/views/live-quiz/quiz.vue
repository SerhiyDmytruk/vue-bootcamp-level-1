<script setup lang="ts">
import { ref, computed, watch, onUnmounted } from "vue";
import QuizQuestion from "@/components/internal/QuizQuestion.vue";
import { useUser } from "@/composables/useUser";
import { useSupabase } from "@/composables/useSupabase";
import QuizTimer from "@/components/internal/QuizTimer.vue";
import { useConfetti } from "@/composables/useConfetti";
import type { Question, ActiveQuestion } from "@/types";
import { useLocalStorage } from "@vueuse/core";
import { config } from "@/assets/live-quiz-questions/config";

const supabase = useSupabase();
const { session, login } = useUser();

const submitted = useLocalStorage("question-submitted", false);
const activeQuestion = ref<ActiveQuestion>();

//#region Question Content Logical Concern
const questions = ref<Question[]>();
const currentQuestion = computed(() => {
  return (questions.value || []).find(
    (x) => x.id === activeQuestion?.value?.question,
  );
});
function getAllQuestions() {
  supabase
    .from("questions")
    .select("*")
    .limit(config.numberOfRealQuestions + config.numberOfExampleQuestions)
    .then(({ data, error }) => {
      if (!error) {
        questions.value = data;
      }
    });
}
getAllQuestions();
// #endregion

//#region Active Question Logical Concern

async function getActiveQuestion() {
  const { data, error } = await supabase
    .from("activeQuestion")
    .select("*")
    .single();
  if (!error) {
    activeQuestion.value = {
      ...data,
      begin_at: new Date(data.begin_at),
    };
  }
}
getActiveQuestion();
const activeQuestionChannel = supabase.channel("activeQuestion");

activeQuestionChannel
  .on(
    "postgres_changes",
    { event: "UPDATE", schema: "public", table: "activeQuestion" },
    (payload) => {
      activeQuestion.value = {
        ...payload.new,
        begin_at: new Date(payload.new.begin_at),
      } as ActiveQuestion;
    },
  )
  .subscribe();

// Reset the submitted flag if the question changes
watch(
  () => activeQuestion.value?.question,
  (newValue, oldValue) => {
    // If no old value then we're just starting up
    // and there's no need to reset the submitted flag
    // or the user refreshed the page before the quiz was finished
    // and we don't want to reset the submitted flag
    // we want to keep there submitted status that's in local storage
    if (!oldValue) return;
    submitted.value = false;
  },
);

onUnmounted(() => {
  activeQuestionChannel.unsubscribe();
});

//#endregion

//#region Answers Logical Concern
type SubmittedAnswer = {
  answer: "a" | "b" | "c" | "d" | null;
  isCorrect: boolean;
  questionId?: number;
};

const quizQuestionEl = ref();

const answers = useLocalStorage<SubmittedAnswer[]>("quiz-answers", []);

function handleQuizQuestionSubmit(answer: SubmittedAnswer) {
  if (!answer.answer) {
    quizQuestionEl.value?.showEmptyAnswerMessage();
    return;
  }
  handleSubmit(answer);
}
async function handleSubmit(answer: SubmittedAnswer) {
  answers.value.push(answer);
  const { data, error } = await supabase
    .from("answers")
    .insert([
      {
        question: activeQuestion.value?.question,
        answer: answer.answer,
        user_id: session.value?.user.id,
        isCorrect: answer.isCorrect,
      },
    ])
    .select();

  if (error) {
    alert(error.message);
  } else {
    submitted.value = true;
  }
}

// submit and empty answer if the user runs out of time
watch(
  () => activeQuestion.value?.status,
  (newStatus, oldStatus) => {
    if (newStatus === "TimesUp" && !submitted.value) {
      handleSubmit({
        answer: null,
        isCorrect: false,
        questionId: activeQuestion.value?.question,
      });
    }
  },
);

const rightAnswers = computed(() => {
  return answers.value.filter((x) => x.isCorrect);
});

const rightAnswersWithoutExampleQuestions = computed(() => {
  return rightAnswers.value.filter(
    (x) => (x.questionId || 0) > config.numberOfExampleQuestions,
  );
});

// Reset the answers and submit status if the quiz starts over
watch(
  () => activeQuestion.value?.status,
  () => {
    if (activeQuestion.value?.status === "NotStarted") {
      answers.value = [];
      submitted.value = false;
    }
  },
);
//#endregion

//#region User Hints/Messages Logical Concern
const message = computed(() => {
  return {
    NotStarted: "",
    BeingRead: "Listen to the question from the instructor",
    Answering: submitted.value
      ? "Your submission has been received. Please wait for others to finish."
      : "Select your answer and submit",
    TimesUp: "Wait for instructor to reveal the answer",
    ShowingAnswer: answers.value.at(-1)?.isCorrect
      ? "You got it right!"
      : answers.value.at(-1)?.answer
        ? "Sorry, you got this one wrong"
        : "You didn't answer in time (or didn't press submit)",
    Finished: "",
  }[activeQuestion.value?.status || "NotStarted"];
});
const messageIcon = computed(() => {
  return {
    NotStarted: "",
    BeingRead: "fa:volume-up",
    Answering: submitted.value ? "fa:check" : "fa:question",
    TimesUp: "fa:clock-o",
    ShowingAnswer: answers.value.at(-1)?.isCorrect ? "fa:check" : "fa:times",
    Finished: "",
  }[activeQuestion.value?.status || "NotStarted"];
});

const messageIconClass = computed(() => {
  return {
    NotStarted: "",
    BeingRead: "text-info",
    Answering: "text-success",
    TimesUp: "text-warning",
    ShowingAnswer: answers.value.at(-1)?.isCorrect
      ? "text-success"
      : "text-error",
    Finished: "",
  }[activeQuestion.value?.status || "NotStarted"];
});
//#endregion

//#region Finished Quiz Logical Concern
const { addConfetti } = useConfetti();
const passed = computed(() => {
  if (activeQuestion.value?.status !== "Finished") return null;
  return rightAnswers.value.length >= 25;
});
watch(
  () => activeQuestion.value?.status,
  () => {
    if (activeQuestion.value?.status === "Finished" && passed.value) {
      addConfetti();
    }
  },
);
//#endregion

// #region Handle Brand New Quiz
const quizId = useLocalStorage("quiz-id", "");
const unwatchQuizIdChecker = watch(
  () => activeQuestion.value?.quiz_id,
  (quiz_id) => {
    if (quiz_id !== quizId.value) {
      quizId.value = quiz_id;
      answers.value = [];
      submitted.value = false;
    }
    unwatchQuizIdChecker();
  },
);

// #endregion
</script>

<template>
  <div class="mt-[60px] p-10 max-w-7xl m-auto relative">
    <!-- Quiz Before Start -->
    <h1
      v-if="activeQuestion?.status === 'NotStarted'"
      class="viewport-center mt-[-100px] text-2xl"
    >
      <span v-if="session"> Waiting for Instructor to Begin the Quiz... </span>
      <span v-else class="text-center">
        Please login to take the quiz
        <br />
        <button @click="login" class="mt-4 btn btn-primary btn-sm">
          Login with <Icon icon="fa:github"></Icon>Github
        </button>
      </span>
    </h1>

    <!-- Quiz Finished-->
    <div
      v-else-if="activeQuestion?.status === 'Finished'"
      class="text-center viewport-center mt-[-100px]"
    >
      <div>
        <h1 class="mb-3 text-5xl">The Quiz is Finished</h1>
        <h2 class="mb-6 text-2xl">
          You would have
          <strong
            class="font-bold"
            :class="{
              'text-success': passed,
              'text-error': !passed,
            }"
            >{{ passed ? "passed" : "failed" }}</strong
          >
          the multiple choice portion of the exam
        </h2>
        <h3 class="text-3xl">
          You got
          <strong class="text-primary">{{
            rightAnswersWithoutExampleQuestions.length
          }}</strong>
          out of
          <strong class="text-primary">{{
            config.numberOfRealQuestions
          }}</strong>
          correct
        </h3>
      </div>
    </div>

    <!-- Quiz In Progress -->
    <div v-else-if="currentQuestion?.content">
      <QuizTimer
        :status="activeQuestion?.status || 'NotStarted'"
        :beginAt="activeQuestion?.begin_at"
        :seconds="activeQuestion?.time_limit_seconds || 10"
      />
      <QuizQuestion
        ref="quizQuestionEl"
        :content="currentQuestion?.content"
        :is-example="currentQuestion.id <= config.numberOfExampleQuestions"
        :number="
          currentQuestion.id <= config.numberOfExampleQuestions
            ? currentQuestion?.id
            : currentQuestion?.id - config.numberOfExampleQuestions
        "
        @submit="
          handleQuizQuestionSubmit({
            ...$event,
            questionId: activeQuestion?.question,
          })
        "
        :show-correct-answer="activeQuestion?.status === 'ShowingAnswer'"
        :disabled="activeQuestion?.status !== 'Answering' || submitted"
        :message="message"
        :message-icon="messageIcon"
        :message-icon-class="messageIconClass"
        :value="
          answers.find((x) => x.questionId === activeQuestion?.question)?.answer
        "
      />
    </div>
  </div>
</template>
