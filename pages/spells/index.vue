<template>
  <section class="container">
    <div class="filter-header-wrapper">
      <h1 class="filter-header">Spell List</h1>
    </div>
    <PageNav
      @first="pageNumber = 0"
      @last="pageNumber = pageCount - 1"
      @next="pageNumber++"
      @prev="pageNumber--"
      :listLength="filteredSpells.length"
      listWording="spells listed."
      :pageNumber="pageNumber"
      :pageCount="pageCount"
    ></PageNav>
    <div>
      <p v-if="!spells.length">Loading...</p>
      <table v-else class="filterable-table">
        <caption class="sr-only">
          Column headers with buttons are sortable.
        </caption>
        <thead>
          <tr>
            <sortable-table-header
              :current-sort-dir="ariaSort.name"
              @sort="(dir) => sort('name', dir)"
              >Name</sortable-table-header
            >
            <sortable-table-header
              :current-sort-dir="ariaSort.school"
              @sort="(dir) => sort('school', dir)"
              >School</sortable-table-header
            >
            <sortable-table-header
              :current-sort-dir="ariaSort.level_int"
              @sort="(dir) => sort('level_int', dir)"
              >Level</sortable-table-header
            >
            <sortable-table-header
              class="hide-mobile"
              :current-sort-dir="ariaSort.components"
              @sort="(dir) => sort('components', dir)"
              >Components</sortable-table-header
            >
            <th class="spell-table-header-class hide-mobile">Class</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="spell in spellsListed" :key="spell.slug">
            <th>
              <nuxt-link
                tag="a"
                :params="{ id: spell.slug }"
                :to="`/spells/${spell.slug}`"
                class="mr-2"
                :prefetch="false"
              >
                {{ spell.name }}
              </nuxt-link>
              <source-tag
                v-if="
                  spell.document__slug && spell.document__slug !== 'wotc-srd'
                "
                class="hide-mobile ml-0"
                :title="spell.document__title"
                :text="spell.document__slug"
              />
            </th>
            <td>{{ capitalize(spell.school) }}</td>
            <td>{{ spell.level_int }}</td>
            <td class="hide-mobile">
              {{ spell.components }}
            </td>
            <td class="hide-mobile">
              <span
                v-for="(spellclass, index) in spell.spell_lists"
                :key="spellclass"
              >
                <!-- the item in the spell_list list -->
                <span class="spell_lists" @click="filterByClass(spellclass)">{{
                  capitalize(spellclass)
                }}</span>
                <!-- comma after any item that isn't the last -->
                <span v-if="index + 1 < spell.spell_lists.length">, </span>
              </span>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <PageNav
      @first="pageNumber = 0"
      @last="pageNumber = pageCount - 1"
      @next="pageNumber++"
      @prev="pageNumber--"
      :listLength="filteredSpells.length"
      listWording="spells listed."
      :pageNumber="pageNumber"
      :pageCount="pageCount"
    ></PageNav>
  </section>
</template>

<script setup>
// COMPONENTS
import PageNav from '~/components/PageNav.vue';
import SourceTag from '~/components/SourceTag.vue';

// LIBRARIES
import { useMainStore } from '~/store';
import { ref, computed, onMounted } from 'vue';

const store = useMainStore();

// VARIABLES
let filter = ref('');
let currentSortProperty = ref('name');
let currentSortDir = ref('ascending');
let pageNumber = ref(0);

// COMPUTED PROPERTIES
const pageCount = computed(() => {
  return Math.ceil(spells.value.length / 50);
});

const spells = computed(() => {
  return store.allSpells;
});

const spellsListed = computed({
  get: function () {
    let start = pageNumber.value * 50;
    let end = start + 50;
    return filteredSpells.value.slice(start, end);
  },
  set: function () {
    return filteredSpells().value.sort((a, b) => {
      let modifier = 1;
      if (currentSortDir.value === 'descending') {
        modifier = -1;
      }
      if (a[currentSortProperty.value] < b[currentSortProperty.value]) {
        return -1 * modifier;
      }
      if (a[currentSortProperty.value] > b[currentSortProperty.value]) {
        return 1 * modifier;
      }
      return 0;
    });
  },
});

const ariaSort = computed(() => {
  return {
    name: getAriaSort('name'),
    school: getAriaSort('school'),
    level_int: getAriaSort('level_int'),
    components: getAriaSort('components'),
  };
});

onMounted(() => {
  store.loadSpells();
});

// METHODS
const filteredSpells = computed(() => {
  console.log(filter.value);
  return spells.value;
});

function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

function sort(prop, dir) {
  currentSortProperty.value = prop;
  currentSortDir.value = dir;
  spellsListed.value = {};
}

function getAriaSort(columName) {
  if (currentSortProperty.value === columName) {
    return currentSortDir.value === 'ascending' ? 'ascending' : 'descending';
  }
  return null;
}
</script>

<style scoped lang="scss">
@media (max-width: 600px) {
  .hide-mobile {
    display: none;
  }
}
</style>
