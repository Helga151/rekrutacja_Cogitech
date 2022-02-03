<template>
  <div class="overflow-auto">
    <b-table class="table"
		sticky-header="750px" hover fixed head-variant="light" 
		id="my-table"
		:items="api"
		:fields="fields"
		:per-page="perPage"
		:current-page="currentPage"
		sort-icon-left
		small
    >
	<template #cell(author)="data">
		{{ data.item.id }} Olga Gerlich <button class="delete" @click="deleteRow(data.item.id)"><i class="fa fa-trash"></i></button>
	</template>
	<template #cell(body)="data">
		<tr class="read_more"><td v-if="!readMore[data.item.id]" @click="toggleReadMore(data.item.id)" >{{ data.item.body.substring(0, 20) }} ...</td>
		<td v-if="readMore[data.item.id]" @click="readMore[data.item.id] = false" >{{ data.item.body }}</td></tr>
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
			readMore: {},
		}
    },
	mounted() {
		this.fetchData()
	}, 
	methods: {
		fetchData () {
			axios.get("https://jsonplaceholder.typicode.com/posts")
			.then((response) => {
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
			//show all message from api body 
			this.$set(this.readMore, id, true);
		},
		deleteRow(id) {
			//this.api.splice(id, 1);
			this.api = this.api.filter((item, i) => i !== id - 1);
        	this.$emit('input', this.api);
		}
	},
	computed: {
		rows() {
			return this.api.length
		}
	}
}
</script>

<style>
	.table {
		padding: 10px;
		width: 100%;
	}
	.read_more{
		cursor: pointer;
	}
	.delete {
		background-color: DodgerBlue; /* Blue background */
		border: none; /* Remove borders */
		color: white; /* White text */
		margin-left: 20px;
		padding: 12px 16px; /* Some padding */
		font-size: 16px; /* Set a font size */
		cursor: pointer; /* Mouse pointer on hover */
	}
	.delete:hover {
		background-color: RoyalBlue;
	}
</style>