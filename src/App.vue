<template>
  <section>
    <header class="is-size-3 has-text-centered">🚀 Vue.js and RxJS are awesome! 🚀</header>

    <section class="section">
      <button class="button" v-stream:click="click$">Click</button>
      <div class="title">{{random$}}</div>
      <div class="title">{{interval$}}</div>
      <div class="title">{{squared$}}</div>
    </section>
  </section>
</template>

<script>
import { interval } from "rxjs";
import { map, takeWhile, endWith } from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const interval$ = interval(1000).pipe(
      takeWhile(x => x < 6),
      endWith(100)
    );

    const squared$ = interval$.pipe(map(x => x * x));

    const random$ = this.click$.pipe(map(() => Math.random()));

    return {
      interval$,
      random$,
      squared$
    };
  }
};
</script>

<style>
</style>
