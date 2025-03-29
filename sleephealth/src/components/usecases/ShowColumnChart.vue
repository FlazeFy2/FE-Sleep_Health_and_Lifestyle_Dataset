<script setup>
    import { ref, onMounted } from "vue"
    import { readCsv } from "@/utils/data_prepare"
    import O_ColumnChartComponent from "../organisms/O_ColumnChartComponent.vue"
    import { countWordFrequencies } from "@/utils/analyze";

    const labels_job_comparison = ref([])
    const series_job_comparison = ref([])

    onMounted(async () => {
        const filePath = "/src/assets/Sleep_health_and_lifestyle_dataset.csv" 
        try {
            const data_raw = await readCsv(filePath,['Person ID','Occupation'])

            // Exploratory Data Analysis (EDA) - Column Chart Total Person Per Job
            const job_comparison = countWordFrequencies(data_raw['Occupation'])
            labels_job_comparison.value = Object.keys(job_comparison)
            series_job_comparison.value = [
                { name : 'Total Person' , data : Object.values(job_comparison)}
            ]
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>

<template>
    <div class="row">
        <div class="col-lg-6 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Column Chart Total Person Per Job -->
            <O_ColumnChartComponent 
                :series="series_job_comparison" 
                :labels="labels_job_comparison" 
                second_title="Total Person per Job" 
                content="This chart shows the total comparison between job"
            />
        </div>
    </div>
</template>

