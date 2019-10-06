<template>
  <section>
    <header class="is-size-3 has-text-centered">🚀 Vue.js and RxJS are awesome! 🚀</header>

    <section class="section">
      <button class="button" v-stream:click="click$">Click</button>
      <div class="title">{{name$}}</div>
      <div>
        <img :src="logo$" alt>
      </div>
    </section>
  </section>
</template>

<script>
import { from } from "rxjs";
import { map, pluck, switchMap, mapTo, share } from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const loadUrl = url => from(this.$http.get(url)).pipe(pluck("data"));

    const js$ = this.click$.pipe(
      mapTo("http://localhost:3333/api/languages/1"),
      switchMap(loadUrl),
      share()
    );

    const name$ = js$.pipe(pluck("name"));
    const logo$ = js$.pipe(
      pluck("logo"),
      map(file => `http://localhost:3333/public/${file}`)
    );

    return {
      name$,
      logo$
    };
  }
};
</script>

<style>
</style>
