<template>
  <tr>
    <th
      v-for="(columnHeader, columnIndex) in columnHeaders"
      :key="columnIndex"
      :class="{ 'cursor-pointer': columnHeader.sortable }"
      @click="rbSorting(columnIndex)"
    >
      {{ columnHeader.title }}
      <i
        v-if="columnHeader.sortable"
        class="fas"
        :class="{'fa-sort text-gray-500' : columnIndex != currentCol,
                         'fa-sort-up' : columnIndex == currentCol && currentDir === 'asc',
                         'fa-sort-down' : columnIndex == currentCol && currentDir === 'desc'}"
      ></i>
    </th>
  </tr>
</template>
<script>
export default {
  methods: {
    rbSorting(col) {
      if (this.columnHeaders[col].sortable) {
        this.currentDir =
          col !== this.currentCol
            ? "asc"
            : this.currentDir === "asc"
            ? "desc"
            : "asc";
        this.currentCol = col;

        this.$emit(
          "sortheader-onclick",
          this.columnHeaders[col].field + "," + this.currentDir
        );
      }
    }
  },
  props: ["columnHeaders", "sortDirection", "sortIndex"],
  data() {
    return {
      currentCol: this.sortIndex,
      currentDir: this.sortDirection
    };
  }
};
</script>
<style lang="css" scoped>
</style>