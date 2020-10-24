<template>
  <div class="border-t border-gray-400 pt-3">
    <div class="pagination w-full xl:w-2/5 pull-left">
      <select
        v-model="itemsPerPage"
        @change="itemsPerPageChanged($event)"
        class="border border-gray-400"
      >
        <option v-for="(rOption, rIndex) in itemsPerPageOptions" :key="rIndex">{{ rOption }}</option>
      </select>
      <span>{{ showingTitle }} per page.</span>
      Showing {{ formatNumber(paginationData.from) }} to {{ formatNumber(paginationData.to) }} of {{ formatNumber(paginationData.total) }} {{ showingTitle }}.
    </div>
    <ul class="pagination pull-right">
      <li
        class="page_item"
        :class="{ disabled: !paginationData.prev_page_url }"
        @click="getFirstPage()"
      >
        <span>
          <i class="fas fa-angle-double-left"></i>
        </span>
      </li>
      <li
        class="page_item"
        :class="{ disabled: !paginationData.prev_page_url }"
        @click="getPreviousPage()"
      >
        <span>
          <i class="fas fa-angle-left"></i>
        </span>
      </li>

      <li
        v-for="(page, pIndex) in pages"
        :key="pIndex"
        class="page_item"
        :class="{ active: page == paginationData.current_page, elipses: page == '...' }"
        @click="page != '...' ? getPage(page) : getPage(null)"
      >
        <span>{{ page }}</span>
      </li>

      <li
        class="page_item"
        :class="{ disabled: !paginationData.next_page_url }"
        @click="getNextPage()"
      >
        <span>
          <i class="fas fa-angle-right"></i>
        </span>
      </li>
      <li
        class="page_item"
        :class="{ disabled: !paginationData.next_page_url }"
        @click="getLastPage()"
      >
        <span>
          <i class="fas fa-angle-double-right"></i>
        </span>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  computed: {
    pages() {
      let items = [];

      if (this.paginationData.last_page <= 10) {
        for (var $i = 1; $i <= this.paginationData.last_page; $i++) {
          items.push($i);
        }
      } else if (this.paginationData.last_page > 10) {
        if (this.paginationData.current_page <= 5) {
          for (var $i = 1; $i <= 7; $i++) {
            items.push($i);
          }

          items.push("...");
          items.push(this.paginationData.last_page - 1);
          items.push(this.paginationData.last_page);
        } else if (
          this.paginationData.current_page > 5 &&
          this.paginationData.current_page <= this.paginationData.last_page - 5
        ) {
          items.push(1);
          items.push(2);
          items.push("...");

          for (
            var $i = this.paginationData.current_page - 2;
            $i <= this.paginationData.current_page + 2;
            $i++
          ) {
            items.push($i);
          }

          items.push("...");
          items.push(this.paginationData.last_page - 1);
          items.push(this.paginationData.last_page);
        } else {
          items.push(1);
          items.push(2);
          items.push("...");

          for (
            var $i = this.paginationData.last_page - 6;
            $i <= this.paginationData.last_page;
            $i++
          ) {
            items.push($i);
          }
        }
      }

      return items;
    }
  },
  methods: {
    formatNumber(a) {
      if (a == undefined || a == null) {
        return "0";
      }
      return a.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, "$1,");
    },
    getFirstPage() {
      this.$emit("fetch-page", this.paginationData.first_page);
    },
    getLastPage() {
      this.$emit("fetch-page", this.paginationData.last_page);
    },
    getNextPage() {
      var p =
        this.paginationData.current_page < this.paginationData.last_page
          ? this.paginationData.current_page + 1
          : this.paginationData.last_page;
      this.$emit("fetch-page", p);
    },
    getPage(p) {
      if (p) {
        this.$emit("fetch-page", p);
      }
    },
    getPreviousPage() {
      var p =
        this.paginationData.current_page > 1
          ? this.paginationData.current_page - 1
          : this.paginationData.first_page;
      this.$emit("fetch-page", p);
    },
    itemsPerPageChanged(e) {
      if (this.itemsPerPage != this.paginationData.per_page) {
        this.$emit("limit-changed", this.itemsPerPage);
      }
    }
  },
  props: ["filterLimit", "paginationData", "showingTitle"],
  data() {
    return {
      itemsPerPage: this.filterLimit,
      itemsPerPageOptions: [5, 10, 15, 25, 50, 100, 250, 500, 1000]
    };
  }
};
</script>
<style lang="css" scoped>
.page_item {
  cursor: pointer;
}
.page_item.elipses {
  cursor: default;
}
.page_item.elipses > span:hover {
  background-color: #ffffff !important;
}
</style>