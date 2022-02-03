<template>
  <div class="overflow-auto">
    <b-table
		sticky-header="750px" hover fixed head-variant="light" 
		id="my-table"
		:items="api"
		:fields="fields"
		:per-page="perPage"
		:current-page="currentPage"
		small
    >
	<template #cell(author)="data">
		{{ data.item.id }} Olga Gerlich
	</template>
	<template #cell(body)="data" class="read_more">
		<tr><td v-if="!readMore[data.item.id]" @click="toggleReadMore(data.item.id)" style="cursor: pointer;">{{ data.item.body.substring(0, 20) }} ...</td></tr>
		<tr><td v-if="readMore[data.item.id]" @click="readMore[data.item.id] = false" style="cursor: pointer;">{{ data.item.body }}</td></tr>
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
					key: 'title', //key to refer to api data
					label: 'Tytuł', //what will be shown
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
			api: [],
			readMore: {}
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
		toggleReadMore(id) {
			this.$set(this.readMore, id, true);
		}
	},
	computed: {
		rows() {
			return this.api.length
		}
	}
}
</script>

<style></style>