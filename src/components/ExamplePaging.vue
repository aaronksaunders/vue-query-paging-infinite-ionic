<template>
  <template v-if="isLoading && !data">
    <h1><ion-loading/></h1>
  </template>
  <template v-else>
    <p>Current Page: {{ page }}</p>
    <ion-button @click="prevPage" :disabled="isFetching || page === 1">
      {{ isFetching ? "Loading..." : "Prev Page" }}
    </ion-button>

    <ion-button @click="nextPage" :disabled="isFetching">
      {{ isFetching ? "Loading..." : "Next Page" }}
    </ion-button>
    <ion-list>
      <ion-item v-for="item in data" :key="item.login.uuid">
        <ion-label>{{ item.email }}</ion-label>
      </ion-item>
    </ion-list>
  </template>
</template>

<script lang="ts" setup>
import { IonItem, IonLabel, IonList, IonButton, IonLoading } from "@ionic/vue";
import { Ref, ref } from "vue";
import { useQuery } from "@tanstack/vue-query";

/**
 * 
 * @param page - reactive variable
 */
const peopleFetcher = async (page: Ref<number>) => {
  const response = await fetch(
    `https://randomuser.me/api/?page=${page.value}&results=20&seed=abc`
  );
  const data = await response.json();

  // fake delay
  await new Promise((resolve) => {
    setTimeout(() => resolve(true), 1000);
  });
  return data?.results || [];
};

const page = ref(1);
const { isLoading, isError, data, error, isFetching, isPreviousData } =
  useQuery({
    queryKey: ["people", page],
    queryFn: () => peopleFetcher(page),
    keepPreviousData: true,
  });

/**
 * update page ref to force a new query and 
 * get the next page
 */
const nextPage = () => {
  if (!isPreviousData.value) {
    page.value = page.value + 1;
  }
};

/**
 * update page ref to force a new query and 
 * get the previous page
 */
const prevPage = () => {
  page.value = Math.max(page.value - 1, 1);
};
</script>
