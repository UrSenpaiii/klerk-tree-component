<script setup lang="ts">
import {onMounted, ref, watch} from 'vue'
import {initFlowbite} from 'flowbite'
import axios from "axios";
import Loader from "@/components/Loader.vue";
import Node from "@/components/Node.vue";

const nodes = ref<Object>(null)
const allowEmpty = ref<Boolean>(false)
const totalCount = ref<Number>(0)

function fetchNodes() {
  axios.get(`https://www.klerk.ru/yindex.php/v3/event/rubrics?allowEmpty=${allowEmpty.value ? 1 : 0}`)
      .then(({data}) => nodes.value = data)
}

watch(allowEmpty, () => {
  nodes.value = null
  totalCount.value = 0

  fetchNodes()
})

onMounted(() => {
  initFlowbite()
  fetchNodes()
})

function sumSelectedCount(selectedCount) {
  totalCount.value += selectedCount
}
</script>

<template>
  <div class="flex items-center justify-between">
    <div class="font-medium text-gray-300">
      Сумма выделенных элементов:
      <span class="font-bold text-lg">{{ totalCount }}</span>
    </div>
    <div class="flex items-center my-2 gap-2">
      <input id="allow-empty-checkbox" type="checkbox" v-model="allowEmpty"
             class="cursor-pointer text-green-700 rounded focus:ring-green-600 ring-offset-neutral-800 focus:ring-2 bg-neutral-800 border-neutral-600">
      <label for="allow-empty-checkbox" class="cursor-pointer font-medium text-gray-300">
        Отображать пустые рубрики
      </label>
    </div>
  </div>

  <ul class="border border-green-400">
    <Loader v-if="!nodes" class="flex justify-center m-8"/>

    <Node v-for="node in nodes" :key="node.id"
          :node="node"
          @select="sumSelectedCount"
    />
  </ul>
</template>