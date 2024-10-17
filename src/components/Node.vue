<script setup lang="ts">

import {computed, ref, watch} from "vue";

const props = defineProps<{
  node: Object,
  isSelected?: Boolean
}>()
const emit = defineEmits(['select'])

const active = ref<Boolean>(false)
const hasChildren = ref<Boolean>(!!props.node.children?.length)
const selected = ref<Boolean>(props.isSelected)

function toggle() {
  if (hasChildren.value)
    active.value = !active.value
}

watch(() => props.isSelected, (value) => {
  selected.value = value
})

watch(selected, () => {
  let sign = selected.value ? 1 : -1
  select(props.node.count * sign)
})

function select(count) {
  emit('select', count)
}

const countSum = computed(() => {
  function r(node, sum) {
    sum += node.count

    if (node.children)
      return node.children.reduce((acc, child) => r(child, acc), sum)

    return sum
  }

  return r(props.node, 0)
})
</script>

<template>
  <li :class="['p-1 border-l border-green-400', {'border-b': hasChildren}]">
    <div :class="['p-3 flex justify-between items-center gap-x-3 cursor-pointer transition duration-300 hover:bg-green-800 border-green-500',
                {'bg-green-900 border-l-4': active}, {'hover:bg-neutral-800': !hasChildren}]"
         @click.stop="toggle"
    >
      <div>
        <a :href="`https://www.klerk.ru${node.url}`" target="_blank" @click.stop
           class="transition duration-300 hover:text-gray-300 hover:underline hover:font-bold"
        >
          <span class="me-1">{{ node.title }}</span>

          (
          <span>Свой: {{ node.count }}</span> |
          <span>Сумма: {{ countSum }}</span>
          )
        </a>
      </div>

      <input id="select-checkbox" type="checkbox" v-model="selected" @click.stop
             class="cursor-pointer text-green-700 rounded focus:ring-green-600 ring-offset-neutral-800 focus:ring-2 bg-neutral-800 border-neutral-600">
    </div>

    <Node v-show="active" v-for="child in node.children" :key="child.id"
          :node="child"
          :isSelected="selected"
          @select="select"
    />
  </li>
</template>
