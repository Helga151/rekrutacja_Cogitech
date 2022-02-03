<template>
  <div class="overflow-auto">
	<b-col lg="6" class="my-1">
		<b-form-group
			label="Wyszukaj"
			label-for="filter-input"
			label-cols-sm="3"
			label-align-sm="right"
			label-size="sm"
			class="mb-0"
		>
			<b-input-group size="sm">
			<b-form-input
				id="filter-input"
				v-model="filter"
				type="search"
				placeholder="Wpisz frazę, którą chcesz znaleźć"
			></b-form-input>

			<b-input-group-append>
				<b-button variant="outline-primary" :disabled="!filter" @click="filter = ''">Wyczyść</b-button>
			</b-input-group-append>
			</b-input-group>
		</b-form-group>
		</b-col>

		<b-col lg="6" class="my-1">
		<b-form-group
			v-model="sortDirection"
			label="Filtrowanie po"
			description="Zaznaczenie obu filtrów jest jednoznaczne z niezaznaczeniem żadnego - wyszukiwanie po całej tablicy"
			label-cols-sm="3"
			label-align-sm="right"
			label-size="sm"
			class="mb-0"
			v-slot="{ ariaDescribedby }"
		>
			<b-form-checkbox-group
			v-model="filterOn"
			:aria-describedby="ariaDescribedby"
			class="mt-1"
			>
			<b-form-checkbox value="title">Tytule</b-form-checkbox>
			<b-form-checkbox value="body">Treści</b-form-checkbox>
			</b-form-checkbox-group>
		</b-form-group>
	</b-col>

    <b-table class="table"
		sticky-header="750px" hover fixed head-variant="light" 
		id="my-table"
		:items="api"
		:fields="fields"
		:per-page="perPage"
		:current-page="currentPage"
		:filter="filter"
		:filter-included-fields="filterOn"
		sort-icon-left
		small
		@filtered="onFilter"
    >
	<template #cell(author)="data">
		Olga Gerlich <button class="delete" @click="deleteRow(data.item.id)"><i class="fa fa-trash"></i></button>
	</template>
	<template #cell(body)="data">
		<tr class="read_more">
			<td v-if="!readMore[data.item.id]" @click="toggleReadMore(data.item.id)" >{{ data.item.body.substring(0, 20) }} ...</td>
			<td v-if="readMore[data.item.id]" @click="readMore[data.item.id] = false" >{{ data.item.body }}</td>
		</tr>
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
			filter: null,
			filterOn: []
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
			this.api = this.api.filter((item) => item.id !== id);
        	this.$emit('input', this.api);
		},
		onFilter(filteredItems) {
			this.rows = filteredItems.length;
			this.currentPage = 1;
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
		background-color: DodgerBlue;
		border: none; 
		color: white; 
		margin-left: 20px;
		padding: 12px 16px; 
		font-size: 16px; 
		cursor: pointer;
	}
	.delete:hover {
		background-color: RoyalBlue;
	}
</style>