<script setup>
    import { ref, onMounted } from "vue"
    import { readCsv } from "@/utils/data_prepare"
    import O_ColumnChartComponent from "../organisms/O_ColumnChartComponent.vue"
    import { countWordFrequencies } from "@/utils/analyze";

    const labels_job_comparison = ref([])
    const series_job_comparison = ref([])
    const labels_gender_sector_comparison = ref([])
    const series_gender_sector_comparison = ref([])
    const labels_category_sector_comparison = ref([])
    const series_category_sector_comparison = ref([])

    onMounted(async () => {
        const filePath = "/src/assets/Sleep_health_and_lifestyle_dataset.csv" 
        try {
            const data_raw = await readCsv(filePath,['Person ID','Occupation','Gender','Category'])

            // Exploratory Data Analysis (EDA) - Column Chart Total Person Per Job
            const job_comparison = countWordFrequencies(data_raw['Occupation'])
            labels_job_comparison.value = Object.keys(job_comparison)
            series_job_comparison.value = [
                { name : 'Total Person' , data : Object.values(job_comparison)}
            ]

            // Exploratory Data Analysis (EDA) - Column Chart Total Person By Gender in Each Job Sector
            const job_sector = {
                "Medic": ["Doctor", "Nurse"],
                "Engineering": ["Software Engineer", "Engineer"],
                "Scientist": ["Scientist"],
                "Sales": ["Sales Representative", "Salesperson", "Manager"],
                "Legal": ["Lawyer"],
                "Education": ["Teacher"],
                "Finance": ["Accountant"]
            }
            let job_sector_comparison = {}
            Object.keys(job_sector).forEach(sector => {
                job_sector_comparison[sector] = { Male: 0, Female: 0 }
            })

            data_raw['Occupation'].forEach((job, idx) => {
                const gender = data_raw['Gender'][idx]
                job = job.trim().toLowerCase()

                Object.entries(job_sector).forEach(([sector, jobs_list]) => {
                    if (jobs_list.some(jb => jb.toLowerCase() === job)) {
                        job_sector_comparison[sector][gender] += 1
                    }
                })
            })

            labels_gender_sector_comparison.value = Object.keys(job_sector_comparison)

            const sorted_sectors = Object.entries(job_sector_comparison).map(([sector, counts]) => ({
                sector, total: counts.Male + counts.Female, Male: counts.Male, Female: counts.Female
            })).sort((a, b) => b.total - a.total)
            labels_gender_sector_comparison.value = sorted_sectors.map(item => item.sector)
            series_gender_sector_comparison.value = [
                { name: "Total Male", data: labels_gender_sector_comparison.value.map(sector => job_sector_comparison[sector]["Male"]) },
                { name: "Total Female", data: labels_gender_sector_comparison.value.map(sector => job_sector_comparison[sector]["Female"]) }
            ]

            // Exploratory Data Analysis (EDA) - Column Chart Total Person By Category in Each Job Sector
            let job_sector_comparison_category = {}

            data_raw['Occupation'].forEach((job, idx) => {
                const category = data_raw['Category'][idx]
                job = job.trim().toLowerCase()

                Object.entries(job_sector).forEach(([sector, jobs_list]) => {
                    if (jobs_list.some(jb => jb.toLowerCase() === job)) {
                        if (!job_sector_comparison_category[sector]) {
                            job_sector_comparison_category[sector] = {}
                        }
                        if (!job_sector_comparison_category[sector][category]) {
                            job_sector_comparison_category[sector][category] = 0
                        }
                        job_sector_comparison_category[sector][category] += 1
                    }
                })
            })

            const categories = ["Normal", "Normal Weight", "Obese", "Overweight"]
            const sorted_sectors_by_category = {}

            categories.forEach(category => {
                sorted_sectors_by_category[category] = Object.entries(job_sector_comparison_category)
                    .map(([sector, counts]) => ({
                        sector,
                        total: counts[category] || 0
                    }))
                    .sort((a, b) => b.total - a.total)
            })

            labels_category_sector_comparison.value = sorted_sectors_by_category["Normal"].map(item => item.sector)

            series_category_sector_comparison.value = categories.map(category => ({
                name: `Total ${category}`,
                data: sorted_sectors_by_category[category].map(item => item.total)
            }))
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
        <div class="col-lg-6 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Column Chart Total Person Per Gender By Its Sector -->
            <O_ColumnChartComponent 
                :series="series_gender_sector_comparison" 
                :labels="labels_gender_sector_comparison" 
                second_title="Total Person Per Gender By Its Sector" 
                content="This chart shows the total comparison between gender for every sector"
            />
        </div>
        <div class="col-lg-6 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Column Chart Total Person Per Category By Its Sector -->
            <O_ColumnChartComponent 
                :series="series_category_sector_comparison" 
                :labels="labels_category_sector_comparison" 
                second_title="Total Person Per Category By Its Sector" 
                content="This chart shows the total comparison between category for every sector"
            />
        </div>
    </div>
</template>

