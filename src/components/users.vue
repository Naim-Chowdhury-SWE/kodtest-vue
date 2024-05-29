<script>
import { ref, onMounted } from 'vue';
import { Card, CardDescription } from "@/components/ui/card"
import { Pagination, PaginationListItem } from "@/components/ui/pagination"
import { Button } from "@/components/ui/button"

export default {
  name: 'UserList',
  setup() {
    const users = ref([]);
    const loading = ref(true);
    const error = ref(null);
    const currentPage = ref(1);
    const totalPages = ref(1);

    const fetchData = async (page = 1) => {
      loading.value = true;
      error.value = null;
      try {
        const response = await fetch(`https://reqres.in/api/users?page=${page}`);
        const data = await response.json();
        users.value = data.data;
        currentPage.value = data.page;
        totalPages.value = data.total_pages;
      } catch (err) {
        error.value = 'Failed to fetch users';
      } finally {
        loading.value = false;
      }
    };

    const nextPage = () => {
      if (currentPage.value < totalPages.value) {
        fetchData(currentPage.value + 1);
      }
    };

    const prevPage = () => {
      if (currentPage.value > 1) {
        fetchData(currentPage.value - 1);
      }
    };

    const goToPage = (page) => {
      if (page >= 1 && page <= totalPages.value) {
        fetchData(page);
      }
    };

    onMounted(() => {
      fetchData();
    });

    return { users, loading, error, currentPage, totalPages, nextPage, prevPage, goToPage };
    console.log(totalPages.value)
  }

};
</script>

<template>
    <div>
      <h1 class="text-3xl text-center text-white bg-gray-800 p-4 font-semibold tracking-wide">Team Members</h1>
      <div v-if="error">{{ error }}</div>
      <div v-if="users.length">
        <div class="grid grid-rows-1 grid-cols-1 sm:grid-rows-2 sm:grid-cols-2 lg:grid-rows-2 lg:grid-cols-3 gap-8 xl:gap-12 m-8">
        <div v-for="user in users" :key="user.id" class="bg-gray-300 rounded-md">
        <Card>
          <img :src="user.avatar" :alt="`${user.first_name} ${user.last_name}`" class="w-24 h-24 object-cover rounded-full mx-auto mt-4" />
          <CardDescription class="flex flex-col justify-center items-center text-center py-4">
            <p class="text-lg font-semibold">{{ user.first_name }} {{ user.last_name }}</p>
            <a :href="`mailto:${user.email}`" class="text-gray-600 hover:underline max-w-fit">{{ user.email }}</a>
          </CardDescription>
        </Card>
        </div>
        </div>

        <Pagination class="flex justify-center mt-2">
          <PaginationList class="px-8 py-2 flex justify-around">

            <Button 
                @click="prevPage" 
                :class="['px-4 py-2 rounded-lg', currentPage === 1 ? 'bg-gray-200 text-gray-100' : 'bg-gray-800 text-white']"
                :disabled="currentPage === 1"
              >
                Prev
              </Button>
            <PaginationListItem v-for="page in totalPages" :key="page" class="px-2 mx-2">
            <Button @click="goToPage(page)" :class="['px-4 py-4 rounded-lg', page === currentPage ? 'bg-gray-800 text-white' : 'bg-white text-gray-800']">
                {{ page }}
            </Button>
            </PaginationListItem>
            <Button 
                @click="nextPage" 
                :class="['px-2 rounded-lg', currentPage === totalPages ? 'bg-gray-200 text-gray-100' : 'bg-gray-800 text-white']"
                :disabled="currentPage === totalPages"
              >
                Next
              </Button>
          </PaginationList>
        </Pagination>
      </div>
    </div>
  </template>