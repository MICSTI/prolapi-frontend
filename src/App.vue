<template>
  <section>
    <header class="is-size-3 has-text-centered">🚀 Vue.js and RxJS are awesome! 🚀</header>

    <section class="section">
      <button :disabled="disabled$" class="button" v-stream:click="click$">{{loadText$}}</button>
      <div class="title">{{name$}}</div>
      <div>
        <img :src="logo$" alt>
      </div>
    </section>
  </section>
</template>

<script>
import { from, merge } from "rxjs";
import {
  map,
  pluck,
  exhaustMap,
  switchMap,
  mapTo,
  share,
  startWith
} from "rxjs/operators";

export default {
  domStreams: ["click$"],
  subscriptions() {
    const loadUrl = url => from(this.$http.get(url)).pipe(pluck("data"));

    const js$ = this.click$.pipe(
      mapTo("http://localhost:3333/api/languages/1"),
      exhaustMap(loadUrl),
      share()
    );

    const name$ = js$.pipe(pluck("name"));
    const logo$ = js$.pipe(
      pluck("logo"),
      map(file => `http://localhost:3333/public/${file}`)
    );

    const disabled$ = merge(
      this.click$.pipe(mapTo(true)),
      js$.pipe(mapTo(false))
    ).pipe(startWith(false));

    const loadText$ = disabled$.pipe(
      map(disabled => (disabled ? "Loading..." : "Load"))
    );

    return {
      name$,
      logo$,
      disabled$,
      loadText$
    };
  }
};
</script>

<style>
</style>
