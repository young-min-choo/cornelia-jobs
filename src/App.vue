<template>
  <div class="app">
    <!-- <p>hello, {{ name }}. You are currently {{ age }}</p>
    <button @click="changeName('Fritz')">Change Name</button>
    <div>
      <button @click="changeAge(35)">Change Age</button>
    </div> -->
    <p v-for="job in jobs" :key="job.id">
      {{ job.location }}
    </p>
    <!-- <p>{{ jobs[0].location }}</p> -->
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, ref, toRefs } from 'vue';
import Job from './types/Job';
import {v4 as uuidv4} from 'uuid';
export default defineComponent({
  name: 'App',
  components: {
  },
  setup() {
    // // Reactive
    // const state = reactive({
    //   name: "Puffy",
    //   age: 25 as number | string,
    // })

    // return { ...toRefs(state) }

    // // refs
    // const name = ref('Puffy')
    // const age = ref<number | string>(25)

    // return {name, age}
    const jobs = ref<Job[]>([
      {
        title: 'Help The Priest',
        location: 'Cathedral',
        salary: 500,
        id: uuidv4()
      },
      {
        title: 'Defeat Goblins',
        location: 'Outside Cornelia Castle',
        salary: 1500,
        id: uuidv4()
      },
      {
        title: 'Talk To The King',
        location: 'Cornelia Castle 3F',
        salary: 200,
        id: uuidv4()
      },
      {
        title: 'Accept Your Fate',
        location: 'Cornelia Castle 3F',
        salary: 5000,
        id: uuidv4()
      },
    ])

    return {jobs}
  },
  methods: {
    // changeName(name: string) {
    //   this.name = name
    // },
    // changeAge(age: number | string) {
    //   this.age = age
    // }

  }
});
</script>

<style>
</style>
