<template>
  <section>
    <h1>ProLAPI</h1>
    <span>{{interval$}}</span>
    <div class="columns">
      <div class="column is-one-third">
        <div class="list is-hoverable">
          <a class="list-item">Featured</a>
          <a class="list-item">All Posts</a>
          <a class="list-item is-active">Announcements</a>
          <a class="list-item">Business</a>
          <a class="list-item">Community</a>
          <a class="list-item">Data</a>
          <a class="list-item">Editor Tools</a>
          <a class="list-item">Education</a>
          <a class="list-item">Engineering</a>
          <a class="list-item">Events</a>
          <a class="list-item">Policy</a>
        </div>
      </div>
      <div class="column">Second column</div>
    </div>
  </section>
</template>

<script>
import { interval } from "rxjs";

export default {
  subscriptions() {
    return {
      interval$: interval(1000)
    };
  }
};
</script>

<style>
</style>
