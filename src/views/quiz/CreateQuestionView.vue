<!-- CreateQuestionView -->
<template>
  <div class="home">
    <h1>Create Question & Answers</h1>
  </div>
  <!-- QuestionComponent -->
  <div>
    <div v-for="index in questionComponentsCount" :key="index">
      <CreateQuestionMolecule ref="questionComponents" />
    </div>
    <button class="new-question-button" :aria-label="'Add Question'" @click="addQuestion">Add Question</button>
    <button class="new-question-button" :aria-label="'Submit Quiz'" @click="submit">Submit Quiz</button>
  </div>
</template>

<script>
import CreateQuestionMolecule from "@/components/molecules/CreateQuestionMolecule.vue";
import { useAppStore } from "@/services/store/appStore";
import EndpointService from "@/services/server/EndpointService";
import { handleError, handleSuccess } from "@/services/MessageHandlerService";
import {getUserFromToken} from "@/services/auth/TokenService";

export default {
  components: {
    CreateQuestionMolecule,
  },
  data() {
    return {
      questionComponentsCount: 1,
      quizQuestions: [], // To store questions and answers
    };
  },
  methods: {
    /**
     * Adds a new question if all fields are filled in and one correct answer is selected.
     */
    addQuestion() {
      if (this.$refs.questionComponents.some((component) => component.getQuestionFromForm().question === "")) {
        handleError("Please fill in all questions");
        return;
      }

      if (this.$refs.questionComponents.some((component) => component.getQuestionFromForm().answerOptions.some(answer => answer.answer === ""))) {
        handleError("Please fill in all answers");
        return;
      }

      if (this.$refs.questionComponents.some((component) => component.getQuestionFromForm().answerOptions.filter(answer => answer.correct === true).length !== 1)) {
        handleError("Please select one correct answer");
        return;
      }

      this.questionComponentsCount++;
    },
    /**
     * Submits the quiz if all fields are filled in and one correct answer is selected.
     */
    submit() {
      const questionComponents = this.$refs.questionComponents;
      this.quizQuestions = questionComponents.map((component) => component.getQuestionFromForm());
      const store = useAppStore();
      store.checkAuthState();
      const tokenUser = getUserFromToken(localStorage.getItem("auth_token"));

      const formQuiz = {
        creatorId: tokenUser.id || 1,
        category: this.getCategory,
        startDate: new Date().toLocaleDateString('en-CA'),
        duration: 30,
        questions: this.quizQuestions,
      };

      if (formQuiz.questions.some(question => question.question === "")) {
        handleError("Please fill in all questions");
        return;
      }

      if (formQuiz.questions.some(question => question.answerOptions.some(answer => answer.answer === ""))) {
        handleError("Please fill in all answers");
        return;
      }

      if (this.$refs.questionComponents.some((component) => component.getQuestionFromForm().answerOptions.filter(answer => answer.correct === true).length !== 1)) {
        handleError("Please select one correct answer");
        return;
      }

      console.log(JSON.stringify(formQuiz));

      EndpointService.post("quizzes/createQuiz", formQuiz)
        .then((response) => {
          console.log(response);
          if (response.status === 201) {
            handleSuccess("Quiz created successfully");
            const quizId = response.data.id;
            this.$router.push({
              name: "lobby",
              params: { quizIds: quizId },
            });
          }
          else {
            handleError("Something went wrong");
          }
        })
        .catch((error) => {
          console.log(error);
          handleError("Something went wrong");
        });

    },
  },
  computed: {
    getCategory() {
      const store = useAppStore();
      console.log(store.getSelectedCategory());

      return store.getSelectedCategory()?.toUpperCase() || "";
    },
  },
};
</script>
