<script setup>
    import { ref, onMounted } from "vue"
    import { readCsv } from "@/utils/data_prepare"
    import O_PieChartComponent from "@/components/organisms/O_PieChartComponent.vue"
    import { countWordFrequencies } from "@/utils/analyze";

    const labels_gender_comparison = ref([])
    const series_gender_comparison = ref([])
    const labels_bmi_comparison = ref([])
    const series_bmi_comparison = ref([])
    const labels_sleep_disorder_comparison = ref([])
    const series_sleep_disorder_comparison = ref([])

    onMounted(async () => {
        const filePath = "/src/assets/Sleep_health_and_lifestyle_dataset.csv" 
        try {
            const data_raw = await readCsv(filePath,['Gender','BMI Category','Sleep Disorder'])
            
            // Exploratory Data Analysis (EDA) - Pie Chart Share of Gender
            const gender_comparison = countWordFrequencies(data_raw['Gender'])
            labels_gender_comparison.value = Object.keys(gender_comparison)
            series_gender_comparison.value = Object.values(gender_comparison)

            // Exploratory Data Analysis (EDA) - Pie Chart Share of BMI Category
            const bmi_comparison = countWordFrequencies(data_raw['BMI Category'])
            labels_bmi_comparison.value = Object.keys(bmi_comparison)
            series_bmi_comparison.value = Object.values(bmi_comparison)

            // Exploratory Data Analysis (EDA) - Pie Chart Share of Sleep Disorder
            const sleep_disorder_comparison = countWordFrequencies(data_raw['Sleep Disorder'])
            labels_sleep_disorder_comparison.value = Object.keys(sleep_disorder_comparison)
            series_sleep_disorder_comparison.value = Object.values(sleep_disorder_comparison)
        } catch (error) {
            console.error("Failed to load CSV:", error)
        }
    })
</script>

<template>
    <div class="row">
        <div class="col-lg-4 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Pie Chart Gender Comparison -->
            <O_PieChartComponent 
                :series="series_gender_comparison" 
                :labels="labels_gender_comparison" 
                second_title="Gender Comparison" 
                content="This compare total person by its gender"
            />
        </div>
        <div class="col-lg-4 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Pie Chart BMI Category Comparison -->
            <O_PieChartComponent 
                :series="series_bmi_comparison" 
                :labels="labels_bmi_comparison" 
                second_title="Protein Comparison" 
                content="This compare total person by its BMI category"
            />
        </div>
        <div class="col-lg-4 col-md-6 col-sm-12 col-12 mx-auto">
            <!-- Exploratory Data Analysis (EDA) - Pie Chart Sleep Disorder Comparison -->
            <O_PieChartComponent 
                :series="series_sleep_disorder_comparison" 
                :labels="labels_sleep_disorder_comparison" 
                second_title="Sleep Disorder Comparison" 
                content="This compare total person by its sleep disorder"
            />
        </div>
    </div>
</template>

