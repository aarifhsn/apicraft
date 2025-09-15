<script setup>
import axios from 'axios';
import { onMounted, ref } from 'vue';

import { TailwindPagination } from 'laravel-vue-pagination';

const categories = ref({});
const products = ref({});

const name = ref('');

const submit = () => {
    axios
        .post('/api/categories', {
            name: name.value,
        })
        .then((response) => {
            console.log('New Category ID: ' + response.data.data.id);
        })
        .catch((error) => console.log(error.response.data.errors.name));
};

const getCategories = async () => {
    await axios
        .get('/api/categories')
        .then((response) => {
            categories.value = response.data;
        })
        .catch((error) => console.log(error));
};

// const getProducts = async () => {
//     await axios
//         .get("/api/products")
//         .then((response) => {
//             products.value = response.data.data;
//         })
//         .catch((error) => console.log(error));
// };

const getProducts = async (page = 1) => {
    await axios
        .get(`/api/products?page=${page}`)
        .then((response) => {
            products.value = response.data;
        })
        .catch((error) => console.log(error));
};

onMounted(() => {
    getCategories();
    getProducts();
});
</script>

<template>
    <div class="container p-4">
        <div class="my-8">
            <!-- Create a form to add category -->
            <form @submit.prevent="submit">
                <input type="text" v-model="name" placeholder="Category name" />
                <button type="submit">Add Category</button>
            </form>
        </div>
        <div class="grid grid-cols-4 gap-4">
            <div class="space-y-2" v-for="product in products.data" :key="product.id">
                <a href="#">
                    <img src="https://placehold.co/300x400" :alt="product.name" />
                </a>
                <a class="text-xl font-semibold text-slate-500 hover:underline">
                    {{ product.name }}
                </a>
                <p>${{ product.price }}</p>
                <p class="prose-slate">{{ product.description }}</p>
            </div>
        </div>

        <TailwindPagination :data="products" @pagination-change-page="getProducts" class="mt-4" />
    </div>
</template>
