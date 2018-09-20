<template>
  <div class="survey-form survey-form--answer">
    <input v-model="value" @change="updateAnswer" type="text" name="question-answer" class="survey-form_input">
    <button @click="removeAnswer" class="survey-form_button survey-form_button--icon">&#215;</button>
  </div>
</template>

<script>
export default {
  name: 'QuestionAnswer',
  props: {
    id: Number,
    initialValue: String
  },
  data: function() {
    return {
      value: this.initialValue
    }
  },
  methods: {
    removeAnswer() {
      this.$emit('removeAnswer', this.id);
    },

    updateAnswer() {
      this.$emit('updateAnswer', {
        id: this.id,
        value: this.value
      });
    }
  }
}
</script>

<style lang="scss">
.survey-form.survey-form--answer {
  flex-direction: row;
  margin: 0;
  align-items: baseline;

  .survey-form_button--icon {
    margin-left: 10px;
  }

  .survey-form_input {
    flex: 1;
  }
}
</style>
