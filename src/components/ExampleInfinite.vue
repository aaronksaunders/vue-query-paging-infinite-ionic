<template>
  <h1 v-if="isLoading"><ion-loading/></h1>
  <ion-button @click="nextPage" :disabled="isFetching" v-if="hasNextPage">
    {{ isFetching ? "Loading..." : "Load More Data" }}
  </ion-button>
  <ion-list>
    <div v-for="(page, index) in data?.pages" :key="index">
      <ion-item v-for="item in page.pageData" :key="item.login.uuid">
        <ion-label>{{ item.email }}</ion-label>
      </ion-item>
    </div>
  </ion-list>
</template>

<script lang="ts" setup>
import { IonItem, IonLabel, IonList, IonButton, IonLoading } from "@ionic/vue";
import { useInfiniteQuery } from "@tanstack/vue-query";

const fetcher = async ({ pageParam = 1 }) => {
  console.log(pageParam);
  const response = await fetch(
    `https://randomuser.me/api/?page=${pageParam}&results=10&seed=abc`
  );
  const data = await response.json();

  // fake delay
  await new Promise((resolve) => {
    setTimeout(() => resolve(true), 1000);
  });

  // sent the cursor/page value and the results
  // set max to 3 pages of data
  return {
    pageData: data?.results || [],
    cursor: pageParam === 3 ? undefined : pageParam + 1,
  };
};

const {
  data,
  error,
  fetchNextPage,
  hasNextPage,
  isFetching,
  isFetchingNextPage,
  isLoading,
  isError,
} = useInfiniteQuery({
  queryKey: ["people"],
  queryFn: fetcher,
  getNextPageParam: (lastPage, pages) => {
    return lastPage.cursor;
  },
});

const nextPage = () => {
  fetchNextPage();
};
</script>
