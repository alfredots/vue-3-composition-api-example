<template>
  <list-quotes :quotes="quotes" :listen-quotes="listenQuotes" @unlisten="onUnlisten"></list-quotes>
  <div class="mt-2 text-right">
    <cite class="text-small">
      Atualizará novamente <b>{{ nextUpdateTime }} segundos</b>
    </cite>
  </div>
</template>

<script>
import { ref, toRefs, reactive } from "@vue/reactivity";
import ListQuotes from "./ListQuotes";
import { onMounted, onUnmounted, watch } from "@vue/runtime-core";
import api from "@/services/api";

const CURRENT_UPDATE_TIME = 30

export default {
  components: { ListQuotes },
  props: {
    listenQuotes: { type: Array, required: true },
  },
  emits: ['unlisten'],
  setup(props, { emit }) {
    const interval = ref(null);
    const quotes = ref({});
    const nextUpdateTime = ref({});

    const methods = reactive({
      onUnlisten (code) {
        emit('unlisten', code)
      },

      restartInterval () {
        clearInterval(interval.value)
        nextUpdateTime.value = CURRENT_UPDATE_TIME
        interval.value = setInterval(() => {
          nextUpdateTime.value -= 1
          if (nextUpdateTime.value === 0) {
            nextUpdateTime.value = CURRENT_UPDATE_TIME
            this.refresh()
          }
        }, 1000)
      },

      async refresh() {
        const { data } = await api.listen(props.listenQuotes);
        quotes.value = data;
      },
    });

    onMounted(() => {
      methods.refresh()
      methods.restartInterval()
    });

    onUnmounted(() => {
      clearInterval(interval.value)
    })

    watch(props, () => {
      methods.refresh()
    })

    return { ...toRefs(methods), quotes, nextUpdateTime };
  },
};
</script>
