<template>
  <div class="app">
    <header>
      <div class="title">
        <h1>Cornelia Jobs</h1>
      </div>
      <div class="order">
        <button @click="handleClick('title')">Sort by Title</button>
        <button @click="handleClick('location')">Sort by Location</button>
        <button @click="handleClick('salary')">Sort by Salary</button>
        <button @click="handleClick('postTime')">Sort by Posted Time</button>
      </div>
    </header>
    <JobList :jobs="jobs" :order="order" />
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, ref, toRefs } from 'vue';
import JobList from './components/JobList.vue'
import Job from './types/Job';
import OrderTerm from './types/OrderTerm';
import {v4 as uuidv4} from 'uuid';
export default defineComponent({
  name: 'App',
  components: {
    JobList
  },
  setup() {    
    const baseURL = 'http://localhost:3000'
    const jobs = ref<Job[]>([
      {
        title: 'Help The Priest',
        location: 'Cathedral',
        salary: 500,
        id: uuidv4(),
        postTime: new Date(2024,10,15)
      },
      {
        title: 'Defeat Goblins',
        location: 'Outside Cornelia Castle',
        salary: 1500,
        id: uuidv4(),
        postTime: new Date(2024,10,6)
      },
      {
        title: 'Talk To The King',
        location: 'Cornelia Castle 3F',
        salary: 200,
        id: uuidv4(),
        postTime: new Date(2024,10,7)
      },
      {
        title: 'Accept Your Fate',
        location: 'Cornelia Castle 3F',
        salary: 5000,
        id: uuidv4(),
        postTime: new Date(2024,10,8)
      },
    ])
    // console.log(jobs.value.push())
    // fetch(baseURL + '/api/jobs', {
    //   method: 'GET',
    //   headers: {
    //     "Content-type": "application/json",
    //   },

    // }).then((response) => {
    //   response.json()
      
    //   .then(res => {
    //     // console.log(jobs.value)
    //     console.log(res.jobs)
    //     jobs.value.concat(res.jobs)
    //     console.log(jobs.value)
    //   })
    // }).catch(err => {
    //   console.error(err)
    // })
    async function fetchJobs(): Promise<Job[]> {
      try {
        const response = await fetch(baseURL + '/api/jobs', {
          method: 'GET',
          headers: {
            "Content-Type": "application/json",
          },
        });

        // Check if the response is okay (status code in the range 200-299)
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        // Parse the JSON response
        const data: { jobs: Job[] } = await response.json();
        return data.jobs;
      } catch (error) {
        console.error('Error fetching jobs:', error);
        throw error;  // Rethrow the error so it can be handled by the caller
      }
    }
    fetchJobs()
      .then(jobsRetrieved => {
        console.log("Jobs: ", jobsRetrieved);
        // jobsRetrieved.forEach((job:Job) => {
        // })
        jobsRetrieved.forEach((singleJob) => {
          console.log("SingleJob: ", singleJob)
          jobs.value.push(singleJob)
        })
        console.log("Final Job Listing: ", jobs.value)
      })
      .catch(error => {
        console.error("Fetch failed: ", error)
      })
    const order = ref<OrderTerm>('title')
    const handleClick = (term: OrderTerm) => {
      order.value = term
    }

    return {jobs, handleClick, order, baseURL}
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
  header {
    text-align: center;
  }
  header .order {
    margin-top: 20px;
  }
  button {
    margin: 0 10px;
    color: #1195c9;
    border: 3px solid #1195c9;
    background: #d5f0ff;
    padding: 8px 16px;
    border-radius: 4px;
    cursor: pointer;
    font-weight: bold;
  }
</style>
