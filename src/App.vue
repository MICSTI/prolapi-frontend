<template>
  <section>
    <header class="is-size-3 has-text-centered">🚀 Vue.js and RxJS are awesome! 🚀</header>

    <section class="section">
      <button class="button" v-stream:click="click$">Load</button>
      <div v-if="error$">
        <span class="tag is-size-4 is-danger">You appear offline 🙀</span>
        <button class="button" v-stream:click="retry$">Retry</button>
      </div>
      <div class="title">{{name$}}</div>
      <div>
        <img :src="logo$" alt>
      </div>
    </section>
  </section>
</template>

<script>
import { from, merge, of } from "rxjs";
import { ajax } from "rxjs/ajax";
import {
  map,
  pluck,
  exhaustMap,
  switchMap,
  mapTo,
  share,
  startWith,
  tap,
  catchError,
  retryWhen,
  delay
} from "rxjs/operators";

export default {
  domStreams: ["click$", "retry$"],
  subscriptions() {
    const js$ = merge(this.click$, this.retry$).pipe(
      mapTo("http://localhost:3333/api/languages/1"),
      switchMap(url =>
        ajax(url).pipe(
          pluck("response"),
          retryWhen(err => err.pipe(delay(3000)))
        )
      ),
      share()
    );

    const error$ = js$.pipe(map(value => value === "error"));

    const name$ = js$.pipe(pluck("name"));
    const logo$ = js$.pipe(
      pluck("logo"),
      map(file => `http://localhost:3333/public/${file}`)
    );

    return {
      name$,
      logo$,
      error$
    };
  }
};
</script>

<style>
</style>
