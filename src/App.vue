<template>
  <section>
    <div class="columns">
      <div class="column is-one-third">
        <div class="list is-hoverable">
          <a
            v-for="language of languages$"
            :key="language.id"
            @click="select(language.id)"
            class="list-item"
            :class="{ 'is-active': isActive(language) }"
          >{{language.name}}</a>
        </div>
      </div>
      <div class="column" v-if="selectedLanguage$">
        <div class="is-size-2">{{selectedLanguage$.name}}</div>
        <div
          class="is-size-4"
        >created in {{selectedLanguage$.created}} by {{getCreators(selectedLanguage$.creator)}}</div>
        <div class="logo-container">
          <img :src="image$" :alt="selectedLanguage$.name">
        </div>
        <div class="hello-world-container">{{hello$}}</div>
      </div>
    </div>
  </section>
</template>

<script>
import { from, interval, combineLatest } from "rxjs";
import { map, pluck, share, startWith, switchMap } from "rxjs/operators";

export default {
  data() {
    return {
      selectedId: 1
    };
  },
  methods: {
    isActive(lang) {
      if (!this.selectedLanguage$) {
        return false;
      }
      return this.selectedLanguage$.id === lang.id;
    },
    getCreators(creatorArr) {
      if (creatorArr.length === 1) {
        return creatorArr[0];
      }

      return (
        creatorArr.slice(0, creatorArr.length - 1).join(", ") +
        " and " +
        creatorArr[creatorArr.length - 1]
      );
    },
    select(id) {
      this.selectedId = id;
    }
  },
  subscriptions() {
    const sortAlphabetically = prop => (a, b) => {
      const valA = a[prop];
      const valB = b[prop];

      return valA < valB ? -1 : valA > valB ? 1 : 0;
    };

    const languages$ = from(
      this.$http.get("http://localhost:3333/api/languages")
    ).pipe(
      pluck("data"),
      map(val => val.sort(sortAlphabetically("name"))),
      share()
    );

    const activeLanguage$ = this.$watchAsObservable("selectedId").pipe(
      pluck("newValue"),
      startWith(1)
    );

    const selectedLanguage$ = combineLatest(languages$, activeLanguage$).pipe(
      map(([languages, selectedId]) =>
        languages.find(item => item.id === selectedId)
      )
    );

    const image$ = selectedLanguage$.pipe(
      pluck("logo"),
      map(file => `http://localhost:3333/public/${file}`)
    );

    const hello$ = selectedLanguage$.pipe(
      pluck("id"),
      map(id => `http://localhost:3333/api/languages/${id}/hello`),
      switchMap(url => this.$http.get(url)),
      pluck("data")
    );

    return {
      activeLanguage$,
      hello$,
      image$,
      interval$: interval(1000),
      languages$,
      selectedLanguage$
    };
  }
};
</script>

<style>
.logo-container {
  max-width: 40%;
  padding: 1rem 0;
}

.hello-world-container {
  background-color: #000;
  color: #fff;
  margin-right: 1rem;
  padding: 0.5rem;
  white-space: pre;
}
</style>
