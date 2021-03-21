<template>
  <div class="container grid-lg my-2 py-2">
    <div class="card mb-2" v-if="listenQuotes.length > 0">
      <div class="card-header">
        <div class="h4">Acompanhando</div>
      </div>
      <div class="card-body">
        <watch-list-quotes
          quotes="quotes"
          :listen-quotes="listenQuotes"
          @unlisten="onUnlisten"
        ></watch-list-quotes>
      </div>
    </div>

    <div class="card">
      <div class="card-header">
        <h4>Todas as moedas</h4>
      </div>
      <div class="card-body">
        <list-quotes
          :quotes="quotes"
          :listen-quotes="listenQuotes"
          @listen="onListen"
          @unlisten="onUnlisten"
        ></list-quotes>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, toRefs } from "@vue/reactivity";
import api from "@/services/api";
import { onMounted } from "@vue/runtime-core";
import ListQuotes from "./components/ListQuotes";
import WatchListQuotes from "./components/WatchListQuotes";

export default {
  name: "App",
  components: { ListQuotes, WatchListQuotes },
  setup() {
    const data = reactive({
      quotes: {},
      listenQuotes: [],
    });

    onMounted(async () => {
      const response = await api.all();
      data.quotes = response.data;
    });

    function onListen(code) {
      data.listenQuotes.push(code);
    }

    function onUnlisten(code) {
      data.listenQuotes = data.listenQuotes.filter((key) => key !== code);
    }

    return { ...toRefs(data), onListen, onUnlisten };
  },
};
</script>