<template>
  <div class="overflow-auto">
    <b-table
		id="my-table"
		:items="api"
		:fields="fields"
		:per-page="perPage"
		:current-page="currentPage"
		small
    >
	<template #cell(author)>
		Olga Gerlich
	</template>
	</b-table>

	<div class="mt-3">
		<h6 class="text-center">Current Page: {{ currentPage }}</h6>
		<b-pagination
		v-model="currentPage"
		:total-rows="rows"
		:per-page="perPage"
		aria-controls="my-table"
		align="center"
		@change="handlePageChange"
		></b-pagination>
	</div>
  </div>
</template>

<script>
import axios from "axios";
export default {
	name: 'App',
	data() {
		return {
			currentPage: 1, 
			perPage: 10,
			fields: [
				{
					key: 'title', 
					label: 'Tytuł',
					sortable: true
				},
				{
					key: 'body', 
					label: 'Treść',
					sortable: true
				}, 
				{
					key: 'author', 
					label: 'Autor',
					sortable: false
				}				
			],
			api: []
		}
    },
	mounted() {
		this.fetchData()
	}, 
	methods: {
		fetchData () {
			axios.get("https://jsonplaceholder.typicode.com/posts")
			.then((response) => {
				//wyświetlanie body z np 20 znakami, po kliknięciu pokazuje się reszta
				console.log(response.data);
				const api = response.data;
				this.api = api;
			})
			.catch((err) => {
				console.log(err)
			})
		},
		handlePageChange(value) {
			this.page = value;
		},
	},
	computed: {
		rows() {
			return this.api.length
		}
	}
}
</script>

<style></style>