<template>
  <q-table class="q-mt-sm" 
    row-key="name" 
    selection="multiple"
    style="height: 400px"
    :rows-per-page-options="[0]"
    :columns="columns" 
    :rows="rows"
    v-model:selected="selected" 
    separator="cell" 
    virtual-scroll 
    flat 
    bordered>
    <template v-slot:body-cell="props">
      <q-td :props="props">
        <q-tooltip class="bg-indigo" :anchor="props.cell" :offset="[5, 5]">
          {{ props.row[props.col.field] }}
        </q-tooltip>
        {{ props.row[props.col.field] }}
      </q-td>
    </template>
  </q-table>
</template>
<script>
import { onMounted, ref, watch, watchEffect } from 'vue';

export default {
  props: {
    isSelected: {
      type: Array,
      default: () => [],
    },
    columns: {
      type: Array,
      default: () => [],
    },

    rows: {
      type: Array,
      default: () => [],
    },
  },

  setup(props, { emit }) {
    const selected = ref([]);

    onMounted(() => {
      watchEffect(() => {
        selected.value = props.isSelected;
      })
    });
    watch(selected, () => {
      emit('getSelected', selected.value);
    })

    return {
      selected
    };
  },
};
</script>
<style lang="scss" scoped></style>
