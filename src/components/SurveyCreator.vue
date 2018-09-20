<template>
  <form v-on:submit.prevent class='survey-form' >
    <div class="survey-form_header">Create a Survey</div>

    <span v-for="error in errors" class="survey-form_error">{{error}}</span>
    <span v-if="savedSuccessfully" class="survey-form_success">{{successMessage}}</span>

    <div class="survey-form_inner">
      <label for="survey-title" class='survey-form_label'>Title</label>
      <input v-model="title" name="survey-title" type="text" class='survey-form_input' size="150" maxlength="100">

      <label for="survey-description" class='survey-form_label'>Description</label>
      <textarea v-model="description" name="survey-description" class='survey-form_input' cols="30" rows="10" maxlength="500"></textarea>

      <label for="survey-points" class='survey-form_label'>Points</label>
      <input v-model.number="points" name="survey-points" type="number" class='survey-form_input'>

      <label class="survey-form_label">Questions</label>
      <Question v-for="question in questions"  :id="question.id" :initialTitle="question.title" :initialType="question.type" :answers="question.answers" @removeQuestion=removeQuestion @updateQuestion=updateQuestion></Question>
      <button @click="addQuestion" class="survey-form_button survey-form_button--add-question">Add Question &#43;</button>
    </div>
   
    <div class="survey-form_footer">
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
      errors: [],
      successMessage: 'Survey has been created successfully!',
      savedSuccessfully: false
    }
  },
  methods: {
    addQuestion() {
      this.questions.push({
        id: this.questions.length + 1,
        title: '',
        type: '',
        answers: []
      });
      this.savedSuccessfully = false;
    },

    removeQuestion(questionId) {
      var questionIndex = this.questions.findIndex(function(question) {
        return question.id === questionId;
      });

      this.questions.splice(questionIndex, 1);
      this.savedSuccessfully = false;
    },

    updateQuestion(questionData) {
      var questionIndex = this.questions.findIndex(function(question) {
        return question.id === questionData.id;
      });

      this.questions[questionIndex] = questionData;
      this.savedSuccessfully = false;
    },

    clearSurvey() {
      this.title = null;
      this.description = null;
      this.points = null;
      this.questions = [];
      this.savedSuccessfully = false;
    },

    saveSurvey() {
      if(this.validateSurvey()) {
        var questions = [],
            question,
            questionObj,
            answers;

        for(var i=0; i<this.questions.length; i++) {
          question = this.questions[i];
          questionObj = {};

          if(question.answers.length) {
            answers = [];
          
            for(var j=0; j<question.answers.length; j++) {
              answers.push(question.answers[j].value);
            }
        
            questionObj.answers = answers;
          }

          questionObj.title = question.title;
          questionObj.type = question.type;
          questions.push(questionObj);
        }

        var survey = {
          title: this.title,
          description: this.description,
          pointValue: this.points,
          questions: questions
        }

        this.savedSuccessfully = true;
        console.log(JSON.stringify(survey));
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

        if(!question.type) {
          this.errors.push('Each question must have a type.');
        }
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
body {
  margin: 0;
}

.survey-form {
  position: relative;
  display: flex;
  flex-direction: column;

  font-family: sans-serif;
}

form.survey-form {
  height: 100vh;

  > .survey-form {
    flex: 0 1 auto;
    overflow: scroll;
  }
}

.survey-form_header,
.survey-form_footer {
  flex: 0 0 auto;
  
  text-align: center;
}

.survey-form_header {
  border-bottom: 2px solid #000;
  margin: 0 0 25px;
  
  font-size: 1.1em;
  font-weight: bold;
  line-height: 40px;
}

.survey-form_footer {
  display: flex;
  margin: 25px 0 0;

  .survey-form_button {
    flex: 1;
    border: 2px solid #000;
    border-radius: 0;

    font-weight: bold;

    + .survey-form_button {
      border-left: 0;
    }
  }
}

.survey-form_inner {
  display: flex;
  flex: 1;
  flex-direction: column;
  overflow-y: scroll;  
  padding: 0 calc((100% - 720px)/2);
}

.survey-form_error {
  margin: 5px 0;
  padding: 0 calc((100% - 720px)/2);

  color: #d00000;
  
  &:before {
    position: relative;
    bottom: 1px;

    display: inline-block;
    box-sizing: border-box;
    width: 20px;
    border: 1px solid;
    border-radius: 50%;
    margin-right: 5px;
    
    content: '!';
    font-weight: bold;
    text-align: center;
  }

  &:first-of-type {
    margin-top: 0;
  }

  &:last-of-type {
    margin-bottom: 25px;
  }
}

.survey-form_success {
  color: green;
    margin: 5px 0;
  padding: 0 calc((100% - 720px)/2);

  &:before {
    position: relative;
    bottom: 1px;

    display: inline-block;
    box-sizing: border-box;
    width: 21px;
    border: 1px solid;
    border-radius: 50%;
    margin-right: 5px;
    padding-right: 3px;

    content: '✓';
    font-weight: bold;
    text-align: center;
  }

  &:first-of-type {
    margin-top: 0;
  }

  &:last-of-type {
    margin-bottom: 25px;
  }
}

.survey-form_label {
  margin-bottom: 5px;
}

.survey-form_input {
  margin-bottom: 20px;
  flex-shrink: 0;

  font-size: 1em;
}

.survey-form_button {
  flex-shrink: 0;
  box-shadow: none;
  border-color: #444;
  border-radius: 2px;

  font-size: 1em;
  line-height: 40px;
}

.survey-form_button--icon {
  border: 0;
  padding: 0;

  cursor: pointer;
  font-size: 1.2em;
  font-weight: bold;
  line-height: 0;
}

.survey-form_button--add-question {
  margin: 15px 0;
}
</style>
