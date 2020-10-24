<template>
  <div class="w-full">
    <div class="relative w-11/12 pull-left">
      <input
        v-model="searchKeyword"
        @blur="searchBox_onBlur"
        @keyup.enter="searchBox_onEnter"
        @keyup="searchBox_onKeyup"
        type="text"
        placeholder="Search"
        class="border-solid border border-gray-500 pl-12 w-full h-16 rounded-lg hover:shadow-md focus:outline-none focus:shadow-md"
      >

      <div class="absolute top-0 left-0 h-full w-12">
        <i class="fas fa-search my-5 ml-4"></i>
      </div>
      <div class="absolute top-0 right-0 h-full w-12" @click="searchBox_onCancel">
        <i
          class="far fa-times-circle hover:bg-gray-300 text-gray-600 my-3 cursor-pointer p-2 rounded-full"
        ></i>
      </div>
    </div>
    <div class="relative w-1/12 pull-left" style="display:none;">
      <div class="my-2" title="Refresh search index" @click="searchbox_onRefresh">
        <span class="fa-stack text-gray-500 hover:text-blue-500">
          <i class="fas fa-circle fa-stack-2x"></i>
          <i class="fas fa-inverse fa-stack-1x fa-sync"></i>
        </span>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  methods: {
    searchBox_onBlur() {
      this.timerStarted = false;
      if (this.timerObject != null) {
        clearTimeout(this.timerObject);
      }
    },
    searchBox_onCancel() {
      this.searchKeyword = "";
      this.$emit("searchbox-oncancel");
    },
    searchBox_onEnter() {
      this.$emit("searchbox-onenter", this.searchKeyword);
    },
    searchBox_onKeyup() {
      if (this.searchKeyword.length) {
        if (this.timerObject != null) {
          clearTimeout(this.timerObject);
        }
        this.timerStarted = false;
      } else if (!this.timerStarted) {
        this.timerStarted = true;
        this.timerObject = setTimeout(this.searchBox_onCancel, 3000);
      }
    },
    searchbox_onRefresh() {
      this.$emit("searchbox-onrefresh");
    }
  },
  data() {
    return {
      searchKeyword: "",
      timerObject: null,
      timerStarted: false
    };
  }
};
</script>
<style lang="css" scoped>
</style>