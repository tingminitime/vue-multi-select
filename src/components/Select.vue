<script setup>
import { ref, watch, watchEffect } from 'vue';

const props = defineProps({
  options: {
    type: Array,
    default: () => [],
  },
  value: {
    type: Object,
    default: () => { },
  },
  multiple: {
    type: Boolean,
    default: false,
  }
})

const emit = defineEmits(['select-option', 'clear-options'])

const isOpen = ref(false)
const highlightedIndex = ref(0)
const selectRef = ref(null)

const isOptionSelected = (option) => {
  return props.multiple
    ? props.value.some(v => v.value === option.value)
    : props.value === option.value
}

watch(isOpen, () => {
  highlightedIndex.value = 0
})

const handler = (e) => {
  if (e.target !== selectRef.value) return
  switch (e.code) {
    case 'Enter':
    case 'Space':
      if (isOpen.value) {
        emit('select-option', { ...props.options[highlightedIndex.value] })
      }
      isOpen.value = !isOpen.value
      break

    case 'ArrowUp':
    case 'ArrowDown':
      if (!isOpen.value) {
        isOpen.value = true
        break
      }

      const newValue = highlightedIndex.value + (e.code === 'ArrowDown' ? 1 : -1)
      if (newValue >= props.options.length) {
        highlightedIndex.value = 0
      } else if (newValue < 0) {
        highlightedIndex.value = props.options.length - 1
      } else {
        highlightedIndex.value = newValue
      }
      break

    case 'Escape':
      isOpen.value = false

    default:
      break
  }
}

watchEffect((onCleanup) => {
  selectRef.value?.addEventListener('keydown', handler)

  onCleanup(() => {
    selectRef.value?.removeEventListener('keydown', handler)
  })
})

</script>

<template>
  <div
    tabindex="0"
    class="select"
    ref="selectRef"
    @click="isOpen = !isOpen"
    @blur="isOpen = false"
  >
    <div
      v-if="multiple"
      class="value"
    >
      <button
        type="button"
        class="option-badge"
        v-for="v in value"
        :key="v.value"
        @click.stop="$emit('select-option', v)"
      >
        {{ v.label }}
        <span class="remove-btn">&times;</span>
      </button>
    </div>
    <div
      v-else
      class="value"
    >{{ value?.label }}</div>
    <button
      type="button"
      class="clear-btn"
      @click.stop="$emit('clear-options', props.multiple)"
    >
      &times;
    </button>
    <div class="divider"></div>
    <div class="caret"></div>
    <ul
      class="options"
      v-show="isOpen"
    >
      <li
        v-for="(option, index) in props.options"
        :key="option.value"
        class="option"
        :class="{ selected: isOptionSelected(option), highlighted: index === highlightedIndex }"
        @click="$emit('select-option', option)"
        @mouseenter="highlightedIndex = index"
      >
        {{ option.label }}
      </li>
    </ul>
  </div>
</template>

<style scoped lang="sass">
$black: #202731


.select
  display: flex
  align-items: center
  position: relative
  width: 20em
  min-height: 1.5em
  border: 0.05em solid #fff
  gap: 0.5em
  padding: 0.5em
  border-radius: 0.25em
  outline: none
  color: white
  &:focus
    border-color: hsl(200, 100%, 50%)

  .value
    flex-grow: 1
    display: flex
    gap: 0.5em
    flex-wrap: wrap

  .clear-btn
    background: none
    color: #ccc
    border: none
    outline: none
    cursor: pointer
    font-size: 1.25em
    &:focus, &:hover
      color: #eee

  .divider
    background-color: #777
    align-self: stretch
    width: .05em

  .caret
    translate: 0 25%
    border: 0.25em solid transparent
    border-top-color: #777

  .options
    position: absolute
    left: 0
    top: calc(100% + 0.25em)
    max-height: 15em
    width: 100%
    overflow-y: auto
    border: 0.05em solid #777
    border-radius: .25em
    background-color: white
    z-index: 100

  .option
    padding: .25em .5em
    color: $black
    cursor: pointer
    &.selected
      background-color: hsl(200, 100, 70%)
    &.highlighted
      background-color: hsl(200, 100, 50%)

  .option-badge
    display: flex
    align-items: center
    border: 0.05em solid #777
    border-radius: 0.25em
    padding: 0.15em 0.25em
    gap: 0.25em
    cursor: pointer
    background: none
    outline: none
    color: white
    &:hover, &:focus
      background-color: hsl(0, 100%, 90%)
      border-color: hsl(0, 100%, 50%)
      color: $black

    > .remove-btn
      font-size: 1.25em
      color: #777
      &:hover, &:focus
        color: hsl(0, 100%, 50%)

</style>
