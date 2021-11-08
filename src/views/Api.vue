<template>
  <div>
    <h2>Qiita</h2>
    <form v-on:submit.prevent>
      <p>
        <label>
          <input type="text" v-model="inputText" />
        </label>
      </p>
    </form>

    <p v-if="items === null">Loading...</p>
    <p v-else-if="items.length === 0">Not found</p>

    <p v-if="isError">Network error</p>

    <ul v-if="items">
      <li v-for="(item, key) in items" v-bind:key="key">
        <a v-bind:href="item.url" target="_blank">{{ item.title }}</a> <span>LGTM:{{item.likes_count}}</span>
      </li>
    </ul>
  </div>

<!--  <pre>{{ $data }}</pre>-->
</template>

<script lang="ts">
import { defineComponent } from "vue";
import axios, { AxiosResponse } from "axios";
import { item } from "@/type/qiitaApiResponse";

export default defineComponent({
  data: (): data => ({
    inputText: "",
    items: [],
    isError: false,
    debouncedGetAnswer: () => null,
  }),

  watch: {
    inputText: function (newText: string): void {
      this.inputText = newText;
      this.debouncedGetAnswer();
    },
  },

  mounted: function (this: any) {
    this.debouncedGetAnswer = debounce(this.getAnswer, 1000);
  },

  methods: {
    getAnswer: function () {
      this.isError = false;
      this.items = null;

      const params = {
        query: this.inputText,
        per_page: 20,
      };

      axios
        .get(`https://qiita.com/api/v2/items`, { params: params })
        .then((e: AxiosResponse<item[]>) => (this.items = e.data))
        .catch(() => {
          this.isError = true;
          this.items = [];
        });
    },
  },
});

type data = {
  inputText: string;
  items: item[] | null; // null is loading
  isError: boolean;
  debouncedGetAnswer: () => void;
};

const debounce = (fn: () => void, intervalMicroSecond: number) => {
  let timerId: number;
  return () => {
    clearTimeout(timerId);
    timerId = setTimeout(() => fn(), intervalMicroSecond);
  };
};
</script>

<style scoped></style>
