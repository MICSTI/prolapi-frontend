<template>
  <section>
    <header class="is-size-3 has-text-centered">🚀 Vue.js and RxJS are awesome! 🚀</header>

    <section class="section">
      <b-tabs v-model="activeTab">
        <b-tab-item v-for="lang of languages" :key="lang.id" :label="lang.name">
          <div class="title">{{name$}}</div>
          <div>
            <img :src="logo$" alt>
          </div>
        </b-tab-item>
      </b-tabs>
    </section>
  </section>
</template>

<script>
import { from, merge } from "rxjs";
import {
  map,
  pluck,
  exhaustMap,
  mapTo,
  share,
  startWith
} from "rxjs/operators";

export default {
  data() {
    return {
      activeTab: 0,
      languages: [
        { id: 1, name: "JavaScript" },
        { id: 2, name: "Java" },
        { id: 5, name: "Go" },
        { id: 6, name: "Python" },
        { id: 11, name: "Kotlin" }
      ]
    };
  },
  domStreams: ["click$"],
  subscriptions() {
    const activeTab$ = this.$watchAsObservable("activeTab", {
      immediate: true
    }).pipe(pluck("newValue"));

    const loadUrl = url => from(this.$http.get(url)).pipe(pluck("data"));

    const js$ = activeTab$.pipe(
      map(tabId => this.languages[tabId].id),
      map(id => `http://localhost:3333/api/languages/${id}`),
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
