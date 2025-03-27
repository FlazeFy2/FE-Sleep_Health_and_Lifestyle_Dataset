<template>
    <div class="my-3 me-2 rounded">
        <vue-good-table
            :columns="[
                { label: 'Person ID', field: 'Person ID', sortable: true, sortFn: (x, y) => Number(x) - Number(y) }
            ].concat(columns)"
            :rows="rows"
            :pagination-options="{ enabled: true, perPage: 25, perPageDropdown: [14, 25, 50, 100, 200] }"
            :search-options="{ enabled: true }"
            :sort-options="{ 
                enabled: true, 
                initialSortBy: { field: 'Person ID', type: 'asc', sortable: true, sortFn: (x, y) => Number(x) - Number(y) } 
            }"
            class="custom-table"
        />
    </div>
</template>
  
<script>
    import { VueGoodTable } from "vue-good-table-next"
    import "vue-good-table-next/dist/vue-good-table-next.css"
    
    export default {
        components: { VueGoodTable },
        props: {
            header: {
                type: Array,
                required: true
            },
            body: {
                type: Array,
                required: true
            }
        },  
        computed: {
            columns() {
                return this.header.filter(col => col.field !== "Person ID")
            },
            rows() {
                return this.body
            }
        }
    }
</script>
  