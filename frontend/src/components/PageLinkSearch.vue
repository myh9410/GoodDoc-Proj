<template>
  <ul class="pagination justify-content-center" style="z-index: 1;">
    <li class="page-item" v-if="prev">
      <router-link
        :to="`searchHospital?no=${ (startPageIndex - 1) * listRowCount }&word=${word}`"
        class="page-link"
        aria-label="Previous"
        @click.native="movePage(startPageIndex - 1)"
      >
        <span aria-hidden="true">&laquo;</span>
      </router-link>
    </li>

    <li
      v-for="index in endPageIndex-startPageIndex + 1 "
      :key="index"
      class="page-item"
      :class="{active:( (startPageIndex + index - 1) == currentPageIndex)}"
    >
      <router-link
        :to="`searchHospital?no=${ (startPageIndex + index - 1) * listRowCount }&word=${word}`"
        class="page-link"
        @click.native="movePage(startPageIndex + index - 1)"
      >{{ startPageIndex + index - 1 }}</router-link>
    </li>

    <li class="page-item" v-if="next">
      <router-link
        :to="`searchHospital?no=${ (endPageIndex + 1) * listRowCount }&word=${word}`"
        class="page-link"
        aria-label="Next"
        @click.native="movePage(endPageIndex + 1)"
      >
        <span aria-hidden="true">&raquo;</span>
      </router-link>
    </li>
  </ul>
</template>

<script>
import http from "@/util/http-common";

export default {
  name: "PageLinkSearch",
  data() {
    return {
      totalListItemCount: 0,
      listRowCount: 10,
      pageLinkCount: 10,
      currentPageIndex: 1,

      pageCount: 0,
      startPageIndex: 0,
      endPageIndex: 0,
      prev: false,
      next: false,

      word: this.$route.query.word,
    };
  },
  methods: {
    movePage(param) {
      this.currentPageIndex = param;
      this.initComponent();
    },

    initComponent() {
      http
        .get(`hospitals/count?subject=&sido=&gu=&word=${this.word}`)
        .then(({ data }) => {
          this.totalListItemCount = data;
          this.initUI();
        })
        .catch((error) => {
          alert("에러가 발생했습니다.");
        });
    },
    initUI() {
      this.pageCount = Math.ceil(this.totalListItemCount / this.listRowCount);

      if (this.currentPageIndex % this.pageLinkCount == 0) {
        this.startPageIndex =
          (this.currentPageIndex / this.pageLinkCount - 1) *
            this.pageLinkCount +
          1;
      } else {
        this.startPageIndex =
          Math.floor(this.currentPageIndex / this.pageLinkCount) *
            this.pageLinkCount +
          1;
      }

      if (this.currentPageIndex % this.pageLinkCount == 0) {
        //10, 20...맨마지막
        this.endPageIndex =
          (this.currentPageIndex / this.pageLinkCount - 1) *
            this.pageLinkCount +
          this.pageLinkCount;
      } else {
        this.endPageIndex =
          Math.floor(this.currentPageIndex / this.pageLinkCount) *
            this.pageLinkCount +
          this.pageLinkCount;
      }

      if (this.endPageIndex > this.pageCount) {
        this.endPageIndex = this.pageCount;
      }

      if (this.currentPageIndex <= this.pageLinkCount) {
        this.prev = false;
      } else {
        this.prev = true;
      }

      if (this.endPageIndex >= this.pageCount) {
        this.endPageIndex = this.pageCount;
        this.next = false;
      } else {
        this.next = true;
      }
    },
  },
  watch: {
    currentPageIndex: function () {
      this.initUI();
    },
  },
  created() {
    this.initComponent();
  },
  mounted() {
    this.$router.push(`/searchHospital?no=${this.listRowCount}&word=${this.word}`);
    this.$router.push(`/searchHospitalMap?no=${this.listRowCount}&word=${this.word}`);
  },
};
</script>
