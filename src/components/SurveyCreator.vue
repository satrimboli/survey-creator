<template>
  <form v-on:submit.prevent class='survey-form' >
    <h1>Create a Survey</h1>

    <div class="survey-form_inner">
      <span v-for="error in errors">{{error}}</span>

      <label for="survey-title" class='survey-form_label'>Title</label>
      <input v-model="title" type="text" name="survey-title" class='survey-form_input' size="150" maxlength="100">

      <label for="survey-description" class='survey-form_label'>Description</label>
      <textarea v-model="description" name="survey-description" class='survey-form_input' cols="30" rows="10" maxlength="500"></textarea>

      <label for="survey-points" class='survey-form_label'>Points</label>
      <input v-model.number="points" type="number" name="survey-points" class='survey-form_input'>

      <label>Questions</label>
      <Question v-for="question in questions" @updateQuestion=updateQuestion :initialTitle="question.title" :id="question.id" :initialType="question.type" :answers="question.answers" @removeQuestion=removeQuestion></Question>
      <button @click="addQuestion" class="survey-form_button survey-form_button--question">Add Question &#43;</button>
    </div>
   
    <div class="survey-form--adjacent">
      <button @click=clearSurvey class="survey-form_button">Clear</button>
      <button @click=saveSurvey class="survey-form_button">Save</button>
    </div>
  </form>
</template>

<script>
import Question from './Question.vue';

export default {
  name: 'SurveyCreator',
  components: {
    Question
  },
  data: function() {
    return {
      title: null,
      description: null,
      points: null,
      questions: [],
      errors: [

      ]
    }
  },
  methods: {
    addQuestion() {
      this.questions.push({
        id: this.questions.length,
        type: 'text',
        answers: [],
        title: ''
      });
    },

    removeQuestion(questionId) {
      var questionIndex = this.questions.findIndex(function(question) {
        return question.id === questionId;
      });

      this.questions.splice(questionIndex, 1);
    },

    updateQuestion(questionData) {
      var questionId = questionData.id;
      var questionIndex = this.questions.findIndex(function(question) {
        return question.id === questionId;
      });

      this.questions[questionIndex] = questionData;
      console.log(this.questions[questionIndex])
    },

    clearSurvey() {
      this.title = null;
      this.description = null;
      this.points = null;
      this.questions = []
    },

    saveSurvey() {
      if(this.validateSurvey()) {
        var survey = {
          
        }
      }
    },

    validateSurvey() {
      this.errors = [];

      if(!this.title || this.title.length > 100) {
        this.errors.push('Title must be more than 0 and less than 100 characters.');
      }

      if(!this.description || this.description.length > 500) {
        this.errors.push('Description must be more than 0 and less than 500 characters.');
      }

      if(this.points < 1) {
        this.errors.push('Points must be a positive integer.');
      }
      
      if(this.questions.length < 1) {
        this.errors.push('Must have one or more questions.');
      }

      for(var i=0; i<this.questions.length; i++) {
        var question = this.questions[i];
        if(!question.title || question.length < 250) {
          this.errors.push('Each question title must be more than 0 and less than 250 characters.');
        }
        if((question.type === 'radio' || question.type === 'checkbox') && question.answers.length < 1) {
          this.errors.push('Each question must have at least one answer.');
        } 
      }

      return this.errors.length === 0;
    }
  }
}
</script>

<style lang="scss">
.survey-form {
  position: relative;
  display: flex;
  flex-direction: column;
  // align-items: flex-start;
  
  > * {
    flex-shrink: 0;
  }
  max-width: 720px;
  margin: 0 auto;

  font-family: sans-serif;

  * {
    box-sizing: border-box;
  }
}

.survey-form_label {
  margin-bottom: 5px;
}

.survey-form_input {
  margin-bottom: 20px;
  flex-shrink: 0;
}

.survey-form_button {
  font-size: 1em;
  border-radius: 2px;
  box-shadow: none;
  padding: 10px 15px;
  flex-shrink: 0;
}

.survey-form--adjacent {
  display: flex;
  width: 100%;
  margin: 15px 0;
  flex-shrink: 0;
  flex: 0 0 auto;
  
  .survey-form_button {
    flex: 1;

    &:first-of-type {
    margin-right: 10px;

    }
  }
}

.survey-form_button--icon {
  padding: 5px;
  line-height: 1;
}

.survey-form_button--question {
  margin: 15px 0;
}

form.survey-form {
  height: 100vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;

  h1 {
    flex: 0 0 auto;
  }

  > .survey-form {
    flex: 0 1 auto;
    overflow: scroll;
  }
}

.survey-form_inner {
  display: flex;
  flex-direction: column;
  overflow-y: scroll;
  flex: 0 1 auto;
}
</style>
