<template>
  <section>
    <h1>ProLAPI</h1>
    <span>{{interval$}}</span>
    <div class="columns">
      <div class="column is-one-third">
        <div class="list is-hoverable">
          <a
            v-for="language of languages$"
            :key="language.id"
            @click="select(language)"
            class="list-item"
            :class="{ 'is-active': isActive(language) }"
          >{{language.name}}</a>
        </div>
      </div>
      <div class="column" v-if="selectedLanguage">
        <div>{{selectedLanguage.name}}</div>
        <div>created in {{selectedLanguage.created}} by {{getCreators(selectedLanguage.creator)}}</div>
      </div>
    </div>
  </section>
</template>

<script>
import { from, interval } from "rxjs";
import { map, pluck } from "rxjs/operators";

export default {
  data() {
    return {
      selectedLanguage: null
    };
  },
  methods: {
    isActive(lang) {
      if (!this.selectedLanguage) {
        return false;
      }
      return this.selectedLanguage.id === lang.id;
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
    select(lang) {
      this.selectedLanguage = lang;
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
      map(val => val.sort(sortAlphabetically("name")))
    );

    return {
      interval$: interval(1000),
      languages$
    };
  }
};
</script>

<style>
</style>
