<!-- SearchQuizView -->
<template>
  <div class="container d-flex justify-content-center align-items-center">
    <div class="card searchForm">
      <div class="card-body">
        <div class="input-group">
          <input type="text" class="form-control" placeholder="Search for quizzes" aria-label="Search for quizzes"
            v-model="searchQuery">
          <button class="btn search-button" @click="searchQuiz">Search</button>
        </div>
      </div>
    </div>
  </div>
  <CategoryMolecule @categoryClicked="handleCategoryClicked" />
</template>


<script>
import CategoryMolecule from "@/components/molecules/CategoryMolecule.vue";
import { handleError } from "@/services/MessageHandlerService";
import EndpointService from "@/services/server/EndpointService";

export default {
  name: "SearchQuizView",
  data() {
    return {
      searchQuery: "",
      quiz: "",
    };
  },
  methods: {
    /**
     * Searches for a quiz with the given id.
     */
    searchQuiz() {
      EndpointService.get(`quizzes/${this.searchQuery}`)
        .then((response) => {
          if (response.status === 200) {
            this.quiz = response.data;
            console.log(this.quiz);

            this.$router.push({
              name: "lobby",
              params: { quizIds: this.quiz.id },
            });
          } else {
            handleError("Quiz does not exist.");
          }
        })
        .catch((error) => {
          console.error("Error while fetching quiz:", error);
          handleError("An error occurred while fetching the quiz.");
        });
    },
    /**
     * Searches for a quiz with the given Category.
     */
    handleCategoryClicked(category) {
      EndpointService.get(`quizzes/categories/${category.toUpperCase()}`)
        .then((response) => {
          if (response.data.length === 0) {
            // This is the case when no quizzes were found for the category
            handleError("No quizzes found for this category.");
          } else {
            const quizIds = response.data.map((quiz) => quiz.id).join(",");
            this.$router.push({
              name: "lobby",
              params: { quizIds },
            });
          }
        })
        .catch((error) => {
          console.error("Error while fetching quizzes by category:", error);
          handleError("An error occurred while fetching quizzes by category.");
        });
    },
  },
  components: { CategoryMolecule },
};
</script>