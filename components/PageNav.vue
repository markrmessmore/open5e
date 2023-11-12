<template>
  <div class="flex w-full flex-wrap align-middle">
    <div class="mt-2 flex w-full flex-wrap text-blood md:w-1/2">
      <div class="flex w-full justify-start italic text-blood md:w-1/2">
        Total of {{ listLength }} {{ listWording }}
      </div>
      <div
        v-if="showItemsPerPage"
        class="flex w-full justify-center italic md:justify-start"
      >
        <span class="pr-2">Items Per Page:</span>
        <select
          id="spellsPerPage"
          v-model="spellsPerPage"
          name="spellsPerPage"
          class="w-1/2 rounded-md ring-1 ring-blood focus:ring-2 focus:ring-blood"
          @change="setPerPage()"
        >
          <option value="20">20</option>
          <option value="50">50</option>
          <option value="100">100</option>
          <option value="250">250</option>
        </select>
      </div>
    </div>
    <div class="flex w-full justify-center md:w-1/2 md:justify-end">
      <button
        :disabled="pageNumber == 0"
        @click="$emit('first')"
        class="mt-1 rounded-md border-2 bg-blood p-1 pl-2 text-fog hover:border-blood hover:bg-fog hover:text-blood"
      >
        <Icon name="heroicons:chevron-double-left" class="mr-1"></Icon> First
      </button>
      <button
        :disabled="pageNumber == 0"
        @click="$emit('prev')"
        class="mt-1 rounded-md border-2 bg-blood p-1 pl-1 pr-2 text-fog hover:border-blood hover:bg-fog hover:text-blood"
      >
        <Icon name="heroicons:chevron-left" class="mr-1"></Icon> Prev
      </button>
      <button
        disabled
        class="mt-1 rounded-md border-2 bg-fog p-1 px-2 text-blood"
      >
        Page {{ pageNumber + 1 }}
      </button>
      <button
        :disabled="pageNumber == pageCount - 1"
        @click="$emit('next')"
        class="mt-1 rounded-md border-2 bg-blood p-1 pl-2 text-fog hover:border-blood hover:bg-fog hover:text-blood"
      >
        Next <Icon name="heroicons:chevron-right" class="mr-1"></Icon>
      </button>
      <button
        :disabled="pageNumber == pageCount - 1"
        @click="$emit('last')"
        class="mt-1 rounded-md border-2 bg-blood p-1 pl-2 text-fog hover:border-blood hover:bg-fog hover:text-blood"
      >
        Last <Icon name="heroicons:chevron-double-right" class="mr-1"></Icon>
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref } from 'vue';

let spellsPerPage = ref(50);

const emit = defineEmits(['changePerPage', 'first', 'last', 'next', 'prev']);

defineProps({
  listLength: Number,
  listWording: String,
  pageCount: Number,
  pageNumber: Number,
  showItemsPerPage: Boolean,
});

function setPerPage() {
  emit('changePerPage', spellsPerPage.value);
}
</script>
