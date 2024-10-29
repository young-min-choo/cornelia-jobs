<template>
  <div class="app">
    <header>
      <div class="title">
        <h1><img src="./assets/ff1_warrior.png" alt="title-image" class="title-image">Cornelia Jobs</h1>
      </div>
      <div class="order">
        <button @click="handleClick('title')">Sort by Title</button>
        <button @click="handleClick('location')">Sort by Location</button>
        <button @click="handleClick('salary')">Sort by Salary</button>
        <button @click="handleClick('postTime')">Sort by Posted Time</button>
      </div>
    </header>
    <div>
      <button @click="createFlag = !createFlag">Create Job</button>
    </div>
    <div v-if="createFlag" class="job-input">
      <div class="input-items-wrapper">
        <label for="create-title"> Title </label>
        <input type="text" id="create-title" v-model="newJobInput.title">

        <label for="create-title"> Location </label>
        <input type="text" id="create-location" v-model="newJobInput.location">
  
        <label for="create-title"> Salary </label>
        <input type="number" id="create-salary" v-model="newJobInput.salary">

        <label for="create-description"> Description </label>
        <textarea id="create-description" v-model="newJobInput.description"></textarea>
      </div>
      <button @click="createJob(newJobInput)">Submit</button>
    </div>
    <JobList :jobs="jobs" :order="order" />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref} from 'vue';
import JobList from './components/JobList.vue'
import Job from './types/Job';
import OrderTerm from './types/OrderTerm';
import {v4 as uuidv4} from 'uuid';
import PlaceholderJob from './types/placeholderJob';
export default defineComponent({
  name: 'App',
  components: {
    JobList
  },
  setup() {    
    let createFlag = ref(false);
    const baseURL = 'http://localhost:3000'
    let newJobInput =  ref<PlaceholderJob>({
      id: '',
      title: '',
      location: '',
      description: '',
      salary: 0,
      postTime: new Date()
    })
    const jobs = ref<Job[]>([])
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
    async function createJob(newJob: PlaceholderJob) {
      try {

        newJob.id = uuidv4()
        newJob.postTime = new Date()
        const job:Job = newJob as Job
        console.log("Job: ", job)
        const response = await fetch(baseURL + '/api/jobs/create', {
          method: 'POST',
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(job),
        });

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        // console.log(data)
        jobs.value.push(data)
        newJob.title = ''
        newJob.location = ''
        newJob.salary = 0
        newJob.description = ''
        createFlag.value = false
      } catch (error) {
        console.error("Error creating job:", error);
        throw error
      }
    }
    fetchJobs()
      .then(jobsRetrieved => {
        // console.log("Jobs: ", jobsRetrieved);
        // jobsRetrieved.forEach((job:Job) => {
        // })
        jobsRetrieved.forEach((singleJob) => {
          // console.log("SingleJob: ", singleJob)
          jobs.value.push(singleJob)
        })
        // console.log("Final Job Listing: ", jobs.value)
      })
      .catch(error => {
        console.error("Fetch failed: ", error)
      })
    const order = ref<OrderTerm>('title')
    const handleClick = (term: OrderTerm) => {
      order.value = term
    }

    // const create = () => {
    //   createFlag = !createFlag;
    //   console.log(createFlag.valueOf())
    // }
    return {jobs, handleClick, order, baseURL, createJob, newJobInput, createFlag}
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
  .job-input {
    display: flex;
    justify-content: center;
  }
  .input-items-wrapper {
    display: grid;
  }
  .title-image {
    height: 25px;
    width: 20px;
    margin-right: 5px;
  }
</style>
