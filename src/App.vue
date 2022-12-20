<script setup>
import { ref } from 'vue'
import Select from '@/components/Select.vue'

const options = [
  {
    label: 'First',
    value: 1,
  },
  {
    label: 'Second',
    value: 2,
  },
  {
    label: 'Third',
    value: 3,
  },
  {
    label: 'Fourth',
    value: 4,
  },
  {
    label: 'Fifth',
    value: 5,
  },
]

let multiSelections = ref([{ ...options[0] }])
let singleSelection = ref({ ...options[0] })

function clearOptions(multiple) {
  if (multiple) {
    multiSelections.value = []
  } else {
    singleSelection.value = undefined
  }
}

function setMultiSelectOption(option) {
  const isOptionExist = multiSelections.value.some(o => o.value === option.value)
  if (isOptionExist) {
    multiSelections.value = multiSelections.value.filter(o => {
      return o.value !== option.value
    })
  } else {
    multiSelections.value = [...multiSelections.value, option]
  }
}

function setSingleSelectOption(option) {
  if (option.value !== singleSelection.value.value) {
    singleSelection.value = option
  }
}

</script>

<template>
  <div class="container">
    <Select
      :options="options"
      :value="singleSelection"
      @select-option="setSingleSelectOption"
      @clear-options="clearOptions"
    ></Select>
    <br>
    <Select
      :options="options"
      :value="multiSelections"
      @select-option="setMultiSelectOption"
      @clear-options="clearOptions"
      :multiple="true"
    ></Select>
    <div style="color: white; marginTop: 1rem">
      Reference :
      <a
        href="https://youtu.be/bAJlYgeovlg"
        target="_blank"
        style="color: white;"
      >
        Youtube
      </a>
      <span> - @WebDevSimplified</span>
    </div>
  </div>
</template>

<style scoped lang="sass">
.container
  display: flex
  flex-direction: column
  align-items: center
</style>
