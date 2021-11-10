<template>
  <div id="sortingSystem">
    <b-container>
      <b-row>
        <div class="header-section">
          <h2>
            Sorting Training System
          </h2>

          <div>
            <h4 class="counter" v-if="!showSuccessMessage && showCounter">
              {{minutes}}:{{ secondsFormatted }}
            </h4>
            <h4 class="counter" v-if="showSuccessMessage">
              List Sorted Successfully in {{minutes}}:{{ secondsFormatted }}
            </h4>
          </div>

          <b-button class="sortingButton" @click="showModal()">Start sorting!</b-button>
        </div>

      </b-row>

      <b-row>
        <b-col cols="12">
          <div class="sortingTable table-responsive">
            <p v-if="countOfPeople">
              {{ countOfPeople }} people in the list
            </p>

            <table class="table table-bordered">
              <thead class="thead-default">
              <tr>
                <th>Sl No</th>
                <th>Email</th>
                <th>Name</th>
                <th>Potatoes</th>
              </tr>
              </thead>
              <draggable v-model="trainingList" tag="tbody" @end="checkTableOrder" :options="{disabled : disableDraggable}">
                <tr v-for="(item,index) in trainingList" :key="item.potatoes">
                  <td>{{ index+1 }}</td>
                  <td>{{ item.email }}</td>
                  <td>{{ item.name }}</td>
                  <td>{{ item.potatoes }}</td>
                </tr>
              </draggable>
            </table>
          </div>
        </b-col>
      </b-row>
    </b-container>
    <modal ref="sortModal" @setNumber="getNumber"></modal>
  </div>
</template>

<script>
import draggable from "vuedraggable";
import faker from "faker"
import Modal from "./components/modal";
export default {
  name: "app",

  components: {
    draggable,
    faker,
    'modal' : Modal
  },

  data() {
    return {
      trainingList : [],
      sortedList: [],
      dragging: false,
      minutes: 0,
      time : 0,
      secondsFormatted: 0,
      running : true,
      showSuccessMessage: false,
      showCounter: false,
      disableDraggable: false,
      countOfPeople: null
    }
  },

  mounted() {
    this.minutes = 0
    this.secondsFormatted = 0
    this.time = 0
    this.running = false
  },

  methods:{

    handleStart() {
      this.running = true
      this.update = setInterval(() => {this.updateTime();}, 1000)
    },

    handleStop() {
      this.running = false
      clearInterval(this.update)
    },

    handleReset() {
      clearInterval(this.update)
      this.time = 0
      this.running = false
      this.minutes = 0
      this.secondsFormatted = '00'
    },

    updateTime() {
      this.time = this.time + (this.running ? 1 : 0)
      this.minutes = Math.floor(this.time / 60)
      let seconds = this.time - (this.minutes * 60)
      this.secondsFormatted = `${seconds < 10 ? '0':''}${seconds}`
    },

    showModal () {
      if (this.$refs.sortModal) this.$refs.sortModal.openDialog()
    },

    getNumber (value) {
      this.countOfPeople = value
      this.trainingList = []
      this.showSuccessMessage = false
      this.showCounter = true
      this.disableDraggable = false
      this.generatePerson(value)
      this.sortedList= this.sortArray()
      this.handleReset()
      this.handleStart()
    },

    generatePerson(numberOfTrainee){
      for (let i = 0; i < numberOfTrainee; i++) {
        let uniquePotatoes = faker.unique(faker.datatype.number)
        let name= faker.name.findName()
        let email=faker.internet.email()
        let person = {
          name : name,
          email: email,
          potatoes: uniquePotatoes
        }
        this.trainingList.push(person)
      }
    },

    checkTableOrder(){
      let matched=true
      this.trainingList.forEach(element =>{
        if(element.potatoes !== this.sortedList[this.trainingList.indexOf(element)].potatoes) {
          matched=false
        }
      })
      if(matched){
        this.handleStop()
        this.showSuccessMessage = true
        this.disableDraggable = true
      }
    },

    sortArray() {
      return this.trainingList.slice().sort(function(a, b) {
        return b.potatoes - a.potatoes;
      })
    }
  }
};
</script>

<style lang="scss" scoped>
@import "./scss/main";

#sortingSystem{
  padding: 64px 0;

  .header-section{
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }

  h2{
    font-weight: $fw-Bold;
    margin-bottom: 0;
  }

  .counter{
    margin-bottom: 0;
    color: #4eb565;
  }

  .sortingButton{
    background-color: #FF8D00;
    color: #fff;
    width: 160px;
    border-radius: 5px;
    border: 0;

    &:focus, &:active{
      box-shadow: none;
    }
  }

  .sortingTable{
    font-size: $fs-small;
    background-color: #fff;
    margin-top: 46px;
    padding: 24px 0;
    border: 1px solid #DDDDDD;
    box-shadow: 0 0 4px rgba(0, 0, 0, 0.101972);
    border-radius: 5px;

    p{
      text-align: end;
      font-weight: $fw-Bold;
      padding-right: 24px;
    }

    .table {
      font-weight: $fw-regular;

      thead{
        tr{
          th{
            color: #555555;
            font-weight: $fw-regular;
          }
        }
      }

      tbody{
        border-top: 0;
        tr{
          cursor: grab;
        }
      }
    }
  }
}


@media only screen and (max-width: 767px) {
  #sortingSystem{
    .header-section{
      flex-direction: column;
      justify-content: start;
      align-items: start;

      h2{
        padding-bottom: 8px;
      }
    }
    .counter{
      padding-bottom: 16px;

    }
  }
}
</style>
