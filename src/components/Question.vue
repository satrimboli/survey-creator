<template>
  <div class="survey-form survey-form--question">
    <button @click="removeQuestion" class="survey-form_button survey-form_button--icon">&#215;</button>
    
    <label for="question-title" class="survey-form_label">Title</label>
    <input v-model="title" @change="updateQuestion" type="text" name="question-title" id="" class="survey-form_input">

    <label for="question-type" class="survey-form_label">Type</label>
    <select v-model="type" @change="updateQuestion" name="question-type" id="" class="survey-form_input">
      <option value="text">Textbox</option>
      <option value="radio">Radio Button</option>
      <option value="check">Checkbox</option>
    </select>

    <div class='survey-form_answers' v-if="needsAnswer">
      <label for="question-answer" class="survey-form_label">Answers</label>
      <QuestionAnswer v-for="answer in answers" :id="answer.id" :initialValue="answer.value" @removeAnswer=removeAnswer @updateAnswer=updateAnswer></QuestionAnswer>
      <button @click="addAnswer" class="survey-form_button survey-form_button--answer">Add Answer &#43;</button>
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
    initialType: String,
    answers: {
      type: Array,
      default: function() {
        return []
      }
    },
    initialTitle: String
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
        id: this.answers.length,
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

    removeQuestion() {
      this.$emit('removeQuestion', this.id);
    },

    updateQuestion() {
      this.$emit('updateQuestion', {
        id: this.id,
        type: this.type,
        answers: this.answers,
        title: this.title
      });
    },

    updateAnswer(answerData) {
      var answerId = answerData.id;
      var answerIndex = this.answers.findIndex(function(answer) {
        return answer.id === answerId;
      });

      this.answers[answerIndex] = answerData;

      this.updateQuestion();
    }
  }
}
</script>

<style lang="scss">
.survey-form.survey-form--question {
  border: 1px solid black;
  padding: 45px 15px 15px 15px;
  margin: 10px 0;
  flex-shrink: 0;


  > .survey-form_button--icon {
    position: absolute;
    right: 15px;
    top: 15px;
  }

  .survey-form_answers {
    display: flex;
    flex-direction: column;
  }
}
</style>
