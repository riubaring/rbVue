<template>
  <div id="app">
    <div style="width: 256px; margin: 16px auto;">
      <input type="checkbox" v-model="checkbox1"> Disable Controls
      <br>
      <br>
      <br>
      <br>
      <span>rbDropdown</span>
      <rbDropdown
        @input="setDropdownSelectedItem"
        :options="timeOptions"
        :textAlign="'right'"
        :value="'12:00 AM'"
        :disabled="checkbox1"
      />
      <div class="text-sm" :class="{'text-gray-100': checkbox1}">Selected Item: {{selectedItem}}</div>
      <br>
      <rbDropdown
        label="name"
        @input="setDropdownSelectedItem2"
        :options="locationOptions"
        :placeholder="'Select a location'"
        :disabled="checkbox1"
        :resetAfter="true"
      />
      <div class="text-sm" :class="{'text-gray-100': checkbox1}">Selected Item: {{selectedItem2}}</div>
      <br>
      <br>
      <span>rbNumberUpDown</span>
      <rbNumberUpDown @input="setSelectedNumber" :value="6" :minValue="1"/>
      <span>Selected Number: {{selectedNumber}}</span>
      <br>
      <br>
      <br>
      <br>
      <span>rbNumberUpDown</span>
      <rbNumberUpDown @input="setSelectedNumberB" :value="6" :minValue="1" :disabled="!checkbox1"/>
      <div
        class="text-base"
        :class="{'text-gray-300': !checkbox1}"
      >Selected Number: {{selectedNumberB}}</div>
      <br>
      <br>
      <br>
      <br>
      <rb-modal v-if="showModal" ref="rbModalBody" :verticalAlign="'top'" @close="dialog_onCancel">
        <template slot="body"></template>
      </rb-modal>
      <button @click="buttonShowModal_onClick">ShowModal</button>
    </div>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";
import rbDropdown from "./components/rbDropdown";
import rbModal from "./components/rbModal";
import rbNumberUpDown from "./components/rbNumberUpDown";

export default {
  name: "App",
  components: {
    HelloWorld,
    rbDropdown,
    rbModal,
    rbNumberUpDown
  },
  methods: {
    buttonShowModal_onClick() {
      this.showModal = true;
      //console.log(this.$refs.rbModal);
    },
    dialog_onCancel() {
      this.showModal = false;
    },
    setDropdownSelectedItem(i) {
      this.selectedItem = i;

      if (this.filterSort.indexOf("sortWith") === -1) {
        console.log("found it");
      } else {
        console.log("not found");
      }
    },
    setDropdownSelectedItem2(i) {
      this.selectedItem2 = i.name;
    },
    setSelectedNumber(n) {
      this.selectedNumber = n;
    },
    setSelectedNumberB(n) {
      this.selectedNumberB = n;
    }
  },
  data() {
    return {
      filterSort: "&sortWith=xxx",
      timeOptions: [
        "12:00 AM",
        "12:30 AM",
        "1:00 AM",
        "1:30 AM",
        "2:00 AM",
        "2:30 AM",
        "3:00 AM",
        "3:30 AM",
        "4:00 AM",
        "4:30 AM",
        "5:00 AM",
        "5:30 AM",
        "6:00 AM",
        "6:30 AM",
        "7:00 AM",
        "7:30 AM",
        "8:00 AM",
        "8:30 AM",
        "9:00 AM",
        "9:30 AM",
        "10:00 AM",
        "10:30 AM",
        "11:00 AM",
        "11:30 AM",
        "12:00 PM",
        "12:30 PM",
        "1:00 PM",
        "1:30 PM",
        "2:00 PM",
        "2:30 PM",
        "3:00 PM",
        "3:30 PM",
        "4:00 PM",
        "4:30 PM",
        "5:00 PM",
        "5:30 PM",
        "6:00 PM",
        "6:30 PM",
        "7:00 PM",
        "7:30 PM",
        "8:00 PM",
        "8:30 PM",
        "9:00 PM",
        "9:30 PM",
        "10:00 PM",
        "10:30 PM",
        "11:00 PM",
        "11:30 PM"
      ],
      locationOptions: [
        { id: 1, name: "Lotus" },
        { id: 2, name: "La Paloma" },
        { id: 3, name: "Palm" },
        { id: 4, name: "Redlands" },
        { id: 5, name: "Cajon" },
        { id: 6, name: "Center" },
        { id: 7, name: "Highland" },
        { id: 8, name: "Cypress" },
        { id: 9, name: "Church" },
        { id: 10, name: "Monterey" }
      ],
      abc: 1,
      checkbox1: false,
      selectedItem: null,
      selectedItem2: null,
      selectedNumber: null,
      selectedNumberB: null,
      showModal: false
    };
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
