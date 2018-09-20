<template>
  <div class="survey-form survey-form--question">
    <button @click="removeQuestion" class="survey-form_button survey-form_button--icon">&#215;</button>
    
    <label for="question-title" class="survey-form_label">Title</label>
    <input v-model="title" @change="updateQuestion" name="question-title" type="text" class="survey-form_input">

    <label for="question-type" class="survey-form_label">Type</label>
    <select v-model="type" @change="updateQuestion" name="question-type" class="survey-form_input">
      <option value=""></option>
      <option value="text">Textbox</option>
      <option value="radio">Radio Button</option>
      <option value="check">Checkbox</option>
    </select>

    <div v-if="needsAnswer" class='survey-form_answers'>
      <label for="question-answer" class="survey-form_label">Answers</label>
      <QuestionAnswer v-for="answer in answers" :id="answer.id" :initialValue="answer.value" @removeAnswer=removeAnswer @updateAnswer=updateAnswer></QuestionAnswer>
      <button @click="addAnswer" class="survey-form_button survey-form_button--add-answer">Add Answer &#43;</button>
    </div>
  </div>
</template>

<script>
import QuestionAnswer from './QuestionAnswer.vue';

export default {
  name: 'Question',
  components: {
    QuestionAnswer
  },
  props: {
    id: Number,
    initialTitle: String,
    initialType: String,
    answers: {
      type: Array,
      default: function() {
        return []
      }
    }
  },
  data: function() {
    return {
      type: this.initialType,
      title: this.initialTitle
    }
  },
  computed: {
    needsAnswer() {
      return this.type === 'radio' || this.type=='check';
    }
  },
  methods: {
    addAnswer() {
      this.answers.push({
        id: this.answers.length + 1,
        value: ''
      });

      this.updateQuestion();
    },

    removeAnswer(answerId) {
      var answerIndex = this.answers.findIndex(function(answer) {
        return answer.id === answerId;
      });

      this.answers.splice(answerIndex, 1);
      this.updateQuestion();
    },

    updateAnswer(answerData) {
      var answerIndex = this.answers.findIndex(function(answer) {
        return answer.id === answerData.id;
      });

      this.answers[answerIndex] = answerData;
      this.updateQuestion();
    },

    removeQuestion() {
      this.$emit('removeQuestion', this.id);
    },

    updateQuestion() {
      this.$emit('updateQuestion', {
        id: this.id,
        title: this.title,        
        type: this.type,
        answers: this.answers
      });
    }
  }
}
</script>

<style lang="scss">
.survey-form.survey-form--question {
  flex-shrink: 0;
  border: 1px solid black;
  margin: 10px 0;
  padding: 20px 10px 10px;

  font-size: .9em;

  > .survey-form_button--icon {
    position: absolute;
    right: 10px;
    top: 13px;
  }

  .survey-form_button--add-answer {
    line-height: 30px;
  }

  .survey-form_answers {
    display: flex;
    flex-direction: column;
  }

  :last-child {
    margin-bottom: 5px;
  }
}
</style>
