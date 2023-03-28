<script setup>
import { onBeforeMount, reactive, ref } from "vue";
import axios from "axios";

const dict = reactive({
  session: null,
  time: null,
});

const time_parsed = ref(null);

const axiosGet = (table) => {
  return new Promise((res, rej) => {
    axios({
      method: "get",
      url: "https://api.airtable.com/v0/appx0N15cQbhjThRk/" + table,
      headers: {
        Authorization:
          "Bearer patj1dY8Tm7rCzuBu.1c3478a1ae3873a0dadb6a8ba46c1a0b83e7b1ef337681eac85c3f3c058b72bb",
      },
    }).then((respond) => {
      // console.log(respond.data);
      res(respond.data.records);
    });
  });
};

const getData = () => {
  axiosGet("Agenda?sort%5B0%5D%5Bfield%5D=Order&sort%5B0%5D%5Bdirection%5D=asc").then(
    (result) => {
      dict.session = result;
      console.log(dict.session);
    }
  );
};

const getTimeData = () => {
  axiosGet("Time").then((result) => {
    dict.time = result;
    time_parsed.value = new Date(result[0].fields.Date).toLocaleString();
    console.log(dict.time);
  });
};

onBeforeMount(() => {
  getData();
  getTimeData();
});
</script>

<template>
  <div class="w-screen h-screen relative bg-white">
    <div class="absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 md:w-2/5 w-4/5">
      <div class="w-full text-center flex flex-col space-y-5">
        <h1 class="md:text-5xl text-3xl font-bold">#TheLightneer</h1>
        <h3 class="md:text-xl text-xl w-full" v-if="dict.time && time_parsed">
          {{ dict.time[0].fields.Name }}：<br class="md:hidden" />{{ time_parsed }}
        </h3>
        <div class="border-b"></div>
        <div
          v-if="dict.session"
          class="flex flex-col space-y-3 overflow-y-auto h-96 w-full"
        >
          <div
            v-for="(session, index) of dict.session"
            :key="session.id"
            class="border flex md:flex-row flex-col py-3 px-5 items-center place-content-between md:space-x-5 space-x-0 md:space-y-0 spcae-y-5"
          >
            <div class="text-3xl font-bold">#{{ index }}</div>
            <div class="md:text-left grow md:pr-5 pr-0">
              <div
                class="bg-gray-500 rounded-2xl w-fit py-1 px-3 text-sm text-white md:mx-0 mx-auto"
                v-if="session.fields.Category"
              >
                {{ session.fields.Category }}
              </div>
              <div class="text-lg font-semibold">{{ session.fields.Title }}</div>
              <div
                class="text-sm font-extralight text-gray-500"
                v-if="session.fields.Speaker"
              >
                delivered by {{ session.fields.Speaker }}
              </div>
            </div>
            <div>{{ session.fields.Duration }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
