<template>
  <div id="popUpModal">
    <b-modal id="modal-center" ref="modal" centered>

      <template #modal-header="{ cancel }">
        <h5>How many people?</h5>
        <b-button size="md" class="close-btn" @click="closeModal()">
          x
        </b-button>
      </template>

      <div>
        <label>Enter a number of how many people you want to add to the list.</label>
        <b-form-input
            pattern="[0-9]"
            type="number"
            id="number"
            v-model="number"
            minlength="20"
            maxlength="100"
            required>
        </b-form-input>
        <p v-if="!checkNumberLimited" class="error-msg">{{errorMessage}}</p>
      </div>

      <template #modal-footer="{ cancel, start }">
        <b-button size="lg" class="cancel-btn"  @click="closeModal()">
          Cancel
        </b-button>
        <b-button size="lg" class="start-btn" @click="starSorting()">
          Start
        </b-button>
      </template>

    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'modal',

  data () {
    return {
      number: 20,
      errorMessage: 'Number must be between 20 and 100',
    }
  },

  computed: {
    checkNumberLimited() {
      return (this.number >= 20 && this.number <= 100)
    }
  },

  methods:{

    openDialog() {
      this.$refs.modal.show()
    },

    closeModal(){
      this.number = 20
      this.$refs.modal.hide()
    },

    starSorting(){
      if(this.checkNumberLimited){
        this.$emit('setNumber', this.number)
        this.$refs.modal.hide()
      }
    }
  }
}
</script>

<style lang="scss">
@import "../scss/main";

label{
  font-size: $fs-small;
  font-weight: $fw-regular;
  padding-bottom: 12px;
}

.modal-header{
  h5{
    margin-bottom: 0;
    align-self: center;
    font-weight: $fw-Bold;
  }
}

.modal-body{
  color: #555555;
  padding-bottom: 48px;

  .error-msg{
    color: #B62D2DFF;
    padding: 10px 0;
    margin-bottom: 0;
  }
}

.close-btn, .cancel-btn, .start-btn{
  border: 0;
  border-radius: 5px;
  font-weight: $fw-Bold;
  font-size: $fs-small;

  &:hover, &:active, &:focus{
    box-shadow: none !important;
    color: #999999;
  }
}

.close-btn{
  background-color: #fff !important;
  color: #999999;
  font-size: $fs-medium;
}

.cancel-btn{
  background-color: #EEEEEE !important;
  color: #555555;
  width: 88px;
  height: 40px;
}

.start-btn{
  background-color: #FF8D00 !important;
  color: #fff;
  width: 88px;
  height: 40px;
}
</style>
