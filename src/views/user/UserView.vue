<!-- User View -->
<template>
  <div class="container">
    <div class="row">
      <div class="col-md-8">
        <!-- Edit Quiz Section -->
        <div v-if="editQuizVisible" class="col-md-8">
          <div class="user-container">
            <div class="user-card">
              <div>
                <h1 class="auth-title">Edit Quiz</h1>
              </div>
              <form class="auth-form" @submit.prevent="saveQuizChanges">
                <div class="mb-2">
                  <label for="editQuizDate" class="form-label">Select Start Date</label>
                  <input type="date" class="form-control" id="editQuizDate" v-model="fetchedQuiz.editQuizDate">
                </div>

                <div class="mb-2">
                  <label for="editQuizDuration" class="form-label">Enter Duration (in days)</label>
                  <input type="number" class="form-control" id="editQuizDuration"
                         v-model="fetchedQuiz.editQuizDuration">
                </div>

                <div class="form-actions">
                  <button type="submit" class="btn update-button">Save Changes</button>
                  <button type="button" class="btn delete-button" @click="cancelEditQuiz">Cancel</button>
                </div>
              </form>
            </div>
          </div>
        </div>

        <!-- User Profile Section -->
        <div v-else-if="user.firstName">
          <div class="user-container">
            <div class="user-card">
              <div class="profile-header d-flex align-items-center">
                <img v-if="profilePicture" :src="profilePicture" alt="Profile" class="profile-pic"/>
                <img v-else src="../../assets/default-profile-pic.webp" alt="Default" class="profile-pic"/>
                <input type="file" id="profilePicture" @change="handleFileUpload" accept="image/*">
              </div>

              <form class="auth-form" @submit.prevent="updateUserProfile">

                <div class="row mb-2">
                  <div class="form-group col-md-3">
                    <label class="form-label" for="salutation">Gender</label>
                    <select v-model="user.salutation" class="form-control" id="salutation">
                      <option value="MALE">Male</option>
                      <option value="FEMALE">Female</option>
                      <option value="OTHER">Other</option>
                    </select>
                  </div>

                  <div class="col-md-9" v-if="user.salutation === 'OTHER'">
                    <label class="form-label" for="otherSalutationDetail">Please Specify</label>
                    <input type="text" v-model="user.otherSalutationDetail" class="form-control"
                           id="otherSalutationDetail" maxlength="30">
                  </div>

                  <div class="col-md-4">
                    <label class="form-label" for="firstName">First Name</label>
                    <input type="text" v-model="user.firstName" class="form-control" id="firstName" required>
                  </div>

                  <div class="col-md-5">
                    <label class="form-label" for="lastName">Last Name</label>
                    <input type="text" v-model="user.lastName" class="form-control" id="lastName" required>
                  </div>
                </div>

                <div class="mb-2">
                  <label class="form-label" for="email">E-Mail Address</label>
                  <input type="email" v-model="user.email" class="form-control" id="email" required>
                </div>

                <div>
                  <label class="form-label" for="country">Country</label>
                  <select v-model="user.country" class="form-control" id="country">
                    <option value="AT">Austria</option>
                    <option value="DE">Germany</option>
                    <option value="CH">Switzerland</option>
                    <option value="BE">Belgium</option>
                    <option value="BG">Bulgaria</option>
                    <option value="DK">Denmark</option>
                    <option value="EE">Estonia</option>
                    <option value="FI">Finland</option>
                    <option value="FR">France</option>
                    <option value="GR">Greece</option>
                    <option value="IE">Ireland</option>
                    <option value="IT">Italy</option>
                    <option value="LV">Latvia</option>
                    <option value="LT">Lithuania</option>
                    <option value="LU">Luxembourg</option>
                    <option value="MT">Malta</option>
                    <option value="NL">Netherlands</option>
                    <option value="PL">Poland</option>
                    <option value="PT">Portugal</option>
                    <option value="RO">Romania</option>
                    <option value="SE">Sweden</option>
                    <option value="SK">Slovakia</option>
                    <option value="SI">Slovenia</option>
                    <option value="ES">Spain</option>
                    <option value="CZ">Czech Republic</option>
                    <option value="HU">Hungary</option>
                    <option value="GB">Great Britain</option>
                    <option value="CY">Cyprus</option>
                  </select>
                </div>

                <div>
                  <label class="form-label password-toggle" :class="{ open: showPasswordFields }" @click="togglePasswordDropdown">
                    Change Password
                  </label>
                  <div v-if="showPasswordFields">
                    <div class="mb-2">
                      <label class="form-label" for="password">New Password</label>
                      <input type="password" v-model="user.password" class="form-control" id="password" minlength="8"
                             required>
                    </div>
                    <div class="mb-2">
                      <label class="form-label" for="confirm-password">Confirm New Password</label>
                      <input type="password" v-model="user.confirmPassword" class="form-control" id="confirm-password"
                             minlength="8" required>
                    </div>
                  </div>
                </div>

                <div class="form-actions">
                  <button type="submit" class="btn update-button">Update Profile</button>
                  <button type="button" class="btn delete-button" @click="deleteAccount">Delete Account</button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>

      <!-- Search Quiz Section -->
      <div class="col-md-4 search-bar">
        <div class="card">
          <div class="card-body">
            <div class="input-group">
              <input type="text" class="form-control" placeholder="Search for my quizzes"
                     aria-label="Search for quizzes" v-model="searchQuery">
              <button class="btn search-button" @click="searchQuiz">Search</button>
            </div>
            <button class="btn update-button" name="Search All Quizzes" @click="searchAllQuizzes">Search All Quizzes</button>
          </div>
        </div>
        <div v-if="fetchedQuiz">
          <div class="quiz-details">
            <div class="button-container justify-content-evenly">
              <div v-if="Array.isArray(fetchedQuiz)">
                <div v-for="quiz in fetchedQuiz" :key="quiz.id" class="quiz-card d-flex flex-column justify-content-center align-items-center">
                  {{ quiz.id }}
                  <div>
                    <button class="btn quiz-button" @click="editQuiz(quiz)"> Edit</button>
                    <button class="btn delete-quiz-button" @click="deleteQuiz(quiz.id)">Delete</button>
                  </div>
                </div>
              </div>
              <div v-else>
                <div class="quiz-card d-flex flex-column justify-content-center align-items-center">
                  {{ fetchedQuiz.id }}
                  <div>
                    <button class="btn quiz-button" @click="editQuiz(fetchedQuiz)"> Edit</button>
                    <button class="btn delete-quiz-button" @click="deleteQuiz(fetchedQuiz.id)">Delete</button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<!-- User View Logic-->
<script>
import * as HandlerService from "@/services/MessageHandlerService";
import {handleError, handleSuccess} from "@/services/MessageHandlerService";
import EndpointService from "@/services/server/EndpointService";
import {getUserFromToken} from "@/services/auth/TokenService";
import {useAppStore} from "@/services/store/appStore";

export default {
  name: "UserView",
  /**
   * Data properties
   */
  data() {
    return {
      searchQuery: "",
      quiz: "",
      fetchedQuiz: [],
      showPasswordFields: false,
      user: {
        id: null,
        salutation: "",
        firstName: "",
        lastName: "",
        email: "",
        country: "",
        password: "",
        confirmPassword: "",
        otherSalutationDetail: "",
      },
      editQuizVisible: false,
      updateQuizData: {
        startDate: "",
        duration: 0,
      },
      profilePicture: null,
    };
  },
  /**
   * Lifecycle hook for when the component is created.
   * Loads the user data and initializes the component state.
   */
  created() {
    this.loadUserData();
  },
  methods: {
    /**
     * Toggles the visibility of password fields in the UI.
     */
    togglePasswordDropdown() {
      this.showPasswordFields = !this.showPasswordFields;
    },
    /**
     * Loads user data from the server.
     * Fetches data based on the authenticated user's token.
     */
    loadUserData() {
      const store = useAppStore();
      store.checkAuthState();
      const tokenUser = getUserFromToken(localStorage.getItem("auth_token"));

      EndpointService.get(`users/${tokenUser.id}`)
          .then((response) => {
            if (response.status === 200) {
              this.user = response.data;
              EndpointService.getWithResponseType(`users/${this.user.id}/profile-picture`, 'blob')
                  .then((profilePictureResponse) => {
                    if (profilePictureResponse.status === 200) {
                      let blob = new Blob([profilePictureResponse.data], { type: profilePictureResponse.headers['content-type'] });

                      let reader = new FileReader();
                      reader.onloadend = () => {
                        this.profilePicture = reader.result;
                        console.log('Profile picture loaded successfully.', this.profilePicture);
                      };

                      reader.readAsDataURL(blob);
                    } else {
                      console.error('Error fetching profile picture:', profilePictureResponse);
                    }
                  }).catch((error) => {
                    console.error('Error fetching profile picture:', error);
                });
              } else {
              handleError("User does not exist.");
            }
          })
          .catch((error) => {
            console.error("Error fetching user data:", error);
            handleError("An error occurred while fetching the user.");
          });
    },
    /**
     * Handles the submission of the user profile form.
     * Validates and updates the user's profile information.
     */
    updateUserProfile() {
      if (this.user.password !== this.user.confirmPassword) {
        handleError("Passwords do not match.");
        return;
      }

      const payload = {
        ...this.user,
        ...(this.user.salutation === 'OTHER' && {otherSalutationDetail: this.user.otherSalutationDetail}),
      };

      if (this.showPasswordFields) {
        payload.password = this.user.password;
      }

      const data = {
        file: this.profilePicture,
        user: payload,
      };

      EndpointService.put(`users/${this.user.id}`, data)
          .then(response => {
            console.log(this.user)
            if (response.status === 200) {
              console.log("Profile updated successfully.");
              HandlerService.handleSuccess("Profile updated successfully.");
            } else {
              handleError("Error updating profile.");
            }
          })
          .catch(error => {
            console.error("Error updating profile:", error);
            handleError("An error occurred while updating the profile.");
          });
    },
    /**
     * Handles the deletion of the user's account.
     * Prompts the user for confirmation before deleting the account.
     */
    deleteAccount() {
      if (confirm("Are you sure you want to delete your account? This action cannot be undone.")) {
        EndpointService.delete(`users/${this.user.id}`)
            .then(response => {
              if (response.status === 200 || response.status === 204) {
                console.log("Account deleted successfully.");
                this.logout();
              } else {
                handleError("Error deleting account.");
              }
            })
            .catch(error => {
              console.error("Error deleting account:", error);
              handleError("An error occurred while deleting the account.");
            });
      }
    },
    /**
     * Logs the user out of the application.
     * Clears the authentication token and redirects the user to the home view.
     */
    logout() {
      const store = useAppStore();
      store.logOut();
      this.$router.push({
        name: "home",
      });
    },
    /**
     * Searches for a quiz based on the quiz ID.
     * Fetches the quiz from the server and displays it in the UI.
     */
    searchQuiz() {
      EndpointService.get(`quizzes/${this.searchQuery}/creator/${this.user.id}`)
          .then((response) => {
            if (response.status === 200) {
              this.fetchedQuiz = response.data;
              console.log(this.fetchedQuiz);
            } else {
              handleError("Quiz not found.");
            }
          })
          .catch((error) => {
            console.error("Error while fetching quiz:", error);
            handleError("Quiz not found.");
          });
    },
    /**
     * Searches for all quizzes created by the user.
     * Fetches the quizzes from the server and displays them in the UI.
     */
    searchAllQuizzes() {
      this.fetchedQuiz = null;
      this.editQuizVisible = false;

      EndpointService.get(`quizzes/all/creator/${this.user.id}`)
          .then((response) => {
            if (response.status === 200) {
              this.fetchedQuiz = response.data;
              console.log(this.fetchedQuiz);
            } else {
              handleError("No quizzes found.");
            }
          })
          .catch((error) => {
            console.error("Error while fetching all quizzes:", error);
            handleError("No quizzes found.");
          });
    },
    /**
     * Deletes a quiz based on the quiz ID.
     * Prompts the user for confirmation before deleting the quiz.
     * @param quizId The ID of the quiz to be deleted.
     */
    deleteQuiz(quizId) {
      EndpointService.delete(`quizzes/${quizId}`)
          .then(response => {
            if (response.status === 200 || response.status === 204) {
              console.log('Quiz deleted successfully');
              handleSuccess("Quiz deleted successfully");
            } else {
              handleError('Failed to delete quiz.');
            }
          })
          .catch(error => {
            console.error('Error while deleting quiz:', error);
            handleError('An error occurred while deleting the quiz.');
          });
    },
    /**
     * Displays the edit quiz form in the UI.
     * @param quiz The quiz to be edited.
     */
    editQuiz(quiz) {
      this.fetchedQuiz = quiz;
      console.log("edit quiz");
      this.editQuizVisible = true;
      console.log("edit quiz visible: " + this.editQuizVisible);
    },
    /**
     * Hides the edit quiz form in the UI.
     */
    cancelEditQuiz() {
      this.editQuizVisible = false;
    },
    /**
     * Saves the changes made to the quiz.
     * Validates the quiz data and updates the quiz on the server.
     */
    saveQuizChanges() {
      if (this.validateQuiz()) {
        const quizId = this.fetchedQuiz.id;

        this.updateQuizData.startDate = this.fetchedQuiz.editQuizDate;
        this.updateQuizData.duration = this.fetchedQuiz.editQuizDuration;

        EndpointService.put(`quizzes/${quizId}/startDate/${this.updateQuizData.startDate}/duration/${this.updateQuizData.duration}`)

            .then((response) => {
              if (response.status === 200) {
                console.log('Quiz updated successfully');
                handleSuccess("Quiz updated successfully");
                this.cancelEditQuiz();
              } else {
                handleError('Failed to update quiz.');
              }
            })
            .catch((error) => {
              console.error('Error while updating quiz:', error);
              handleError('An error occurred while updating the quiz.');
            });
      }
    },
    /**
     * Validates the quiz data.
     * @returns {boolean} True if the quiz data is valid, false otherwise.
     */
    validateQuiz() {
      if (this.fetchedQuiz.editQuizDate === "") {
        handleError("Please select a date");
        return false;
      }

      if (this.fetchedQuiz.editQuizDuration === "") {
        handleError("Please enter a duration");
        return false;
      }

      let today = new Date();
      let startDate = new Date(this.fetchedQuiz.editQuizDate);
      if (startDate > today) {
        handleError("Start date should not be in the future");
        return false;
      }

      return true;
    },
    /**
     * Handles the upload of a profile picture.
     * @param event The event containing the uploaded file.
     */
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          this.profilePicture = e.target.result;
        };
        reader.readAsDataURL(file);
      }
    },
  },
};
</script>
