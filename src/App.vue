<template>
  <section>
    <header class="is-size-3 has-text-centered">🚀 Vue.js and RxJS are awesome! 🚀</header>

    <section class="section">
      <button class="button" v-stream:click="click$">Click</button>
      <div class="title">{{random$}}</div>
    </section>
  </section>
</template>

<script>
import { map } from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const random$ = this.click$.pipe(map(() => Math.random()));

    return {
      random$
    };
  }
};
</script>

<style>
</style>
