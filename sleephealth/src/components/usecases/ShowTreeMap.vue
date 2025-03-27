<script setup>
  import { ref, onMounted } from "vue"
  import { readCsv } from "@/utils/data_prepare"
  import { countWordFrequencies } from "@/utils/analyze"
  import O_TreeMapComponent from "@/components/organisms/O_TreeMapComponent.vue"
  
  const series_job_tree = ref([])

  onMounted(async () => {
    const filePath = "/src/assets/Sleep_health_and_lifestyle_dataset.csv" 
    try {
      const data_job_raw = await readCsv(filePath,['Occupation'])

      // Exploratory Data Analysis (EDA) - Tree Map Top 20 Quantity Comparison
      const most_job_tree = countWordFrequencies(data_job_raw['Occupation'], 20)
      series_job_tree.value = [
        {
          data: Object.entries(most_job_tree).map(([key, value]) => ({
            x: key,
            y: value
          }))
        }
      ]
    } catch (error) {
      console.error("Failed to load CSV:", error)
    }
  })
</script>

<template>
  <div class="row">
    <div class="col-lg-6 col-md-6 col-sm-12 col-12 mx-auto">
      <!-- Exploratory Data Analysis (EDA) - Tree Map Occupation / Job Comparison -->
      <O_TreeMapComponent 
        :series="series_job_tree" 
        second_title="Occupation / Job Comparison" 
        content="This show occupation / job comparison by its quantity"
      />
    </div>
  </div>
</template>


