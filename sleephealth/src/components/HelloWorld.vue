<script setup>
  import A_TextComponent from "@/components/atoms/A_TextComponent.vue"
  import O_TableComponent from "@/components/organisms/O_TableComponent.vue"
  import { readBody, readHeader } from "@/utils/data_prepare";
  import { onMounted, ref } from "vue";
  import ShowTreeMap from "@/components/usecases/ShowTreeMap.vue"
  import M_DescriptiveStatistic from "@/components/molecules/M_DescriptiveStatistic.vue"
  import ShowPieChart from "@/components/usecases/ShowPieChart.vue"

  const header_dataset = ref([])
  const body_dataset = ref([])

  onMounted(async () => {
    const filePath = "/src/assets/Sleep_health_and_lifestyle_dataset.csv" 
    try {
      const data_header = await readHeader(filePath)
      const data_body = await readBody(filePath)

      // Data Prepare
      const data_header_clean = data_header.filter(dt => dt !== "Unnamed: 0")
      header_dataset.value = data_header_clean.map(header => ({
        label: header,
        field: header,
        sortable: true
      }))      
      const data_body_clean = data_body.map(row => {
        return Object.fromEntries(
            Object.entries(row).filter(([key]) => data_header_clean.includes(key))
        )
      })
      body_dataset.value = data_body_clean
    } catch (error) {
      console.error("Failed to load CSV:", error)
    }
  })
</script>

<template>
  <div class="greetings">
    <A_TextComponent title="Sleep Health and Lifestyle" />
    <div class="row">
      <div class="col-lg-6 col-md-6 col-sm-12 col-12">
        <A_TextComponent second_title="About" />
        <A_TextComponent content="The Sleep Health and Lifestyle Dataset comprises 400 rows and 13 columns, covering a wide range of variables related to sleep and daily habits. It includes details such as gender, age, occupation, sleep duration, quality of sleep, physical activity level, stress levels, BMI category, blood pressure, heart rate, daily steps, and the presence or absence of sleep disorders."/>
      </div>
      <div class="col-lg-6 col-md-6 col-sm-12 col-12">
        <A_TextComponent second_title="Source" />
        <a href="https://www.kaggle.com/datasets/uom190346a/sleep-health-and-lifestyle-dataset" target="_blank" rel="noopener">Kaggle</a>
        <a href="https://github.com/FlazeFy2/DS-Sleep_Health_and_Lifestyle_Dataset" target="_blank" rel="noopener">Jupiter Notebook</a>
      </div>
    </div>
    
    <hr>
    <div class="d-flex justify-content-start mt-3">
      <button class="accordion-button" type="button" data-bs-toggle="collapse" data-bs-target="#collapseOne" aria-expanded="true" aria-controls="collapseOne">Dataset</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseTwo" aria-expanded="false" aria-controls="collapseTwo">Descriptive Statistic</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseThree" aria-expanded="false" aria-controls="collapseThree">Tree Map</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseFour" aria-expanded="false" aria-controls="collapseFour">Pie Chart</button>
      <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse" data-bs-target="#collapseFive" aria-expanded="false" aria-controls="collapseFive">Column Chart</button>
    </div>

    <div class="accordion" id="accordionExample">
      <div class="accordion-item">
        <div id="collapseOne" class="accordion-collapse collapse show" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Dataset" />
          <O_TableComponent :header="header_dataset" :body="body_dataset"/>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseTwo" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Descriptive Statistic" />
          <div class="row">
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Age"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Sleep Duration"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Quality of Sleep"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Physical Activity Level"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Stress Level"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Heart Rate"/>
            </div>
            <div class="col-lg-6 col-md-6 col-sm-12 col-12">
              <M_DescriptiveStatistic target_col="Daily Steps"/>
            </div>
          </div>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseThree" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Tree Map" />
          <ShowTreeMap/>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseFour" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Pie Chart" />
          <ShowPieChart/>
        </div>
      </div>

      <div class="accordion-item">
        <div id="collapseFive" class="accordion-collapse collapse" data-bs-parent="#accordionExample">
          <A_TextComponent second_title="Column Chart" />
         
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  a {
    color: #42b883;
    font-weight: 500;
    font-size: 14px;
    background-color:rgba(66, 184, 131, 0.25);
    border-radius: 20px;
    padding: 7px 12px;
    margin-right: 6px;
  }
</style>
