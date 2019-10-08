<template>
  <section>
    <header class="is-size-3 has-text-centered">🚀 Vue.js and RxJS are awesome! 🚀</header>

    <section class="section"></section>
  </section>
</template>

<script>
import { from, interval } from "rxjs";
import {
  exhaustMap,
  map,
  mapTo,
  pluck,
  takeWhile,
  share,
  switchMap,
  tap
} from "rxjs/operators";

export default {};
</script>

<style>
</style>
