<template>
  <div class="select-unselect-grid">
    <div>
      <h3>Available Options</h3>
      <div class="column">
        <div
            v-for="option in availableOptions"
            :key="option.id"
            class="option"
            @click="toggleOption(option)"
        >
          {{ option.label }}
        </div>
    </div>
    </div>

    <div>
      <h3>Disabled Options</h3>
      <div class="column">
        <div
            v-for="option in disabledOptions"
            :key="option.id"
            class="option disabled"
            @click="toggleOption(option)"
        >
          {{ option.label }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue';

const props = defineProps({
  field: {
    type: Object,
    required: true,
  },
  modelValue: {
    type: Array,
    default: () => [],
  },
});

const emit = defineEmits(['update']);

const disabledOptionIds = ref(props.modelValue || []);

const availableOptions = computed(() => {
  return props.field.options.filter(
      (option) => !disabledOptionIds.value.includes(option.id)
  );
});

const disabledOptions = computed(() => {
  return props.field.options.filter(
      (option) => disabledOptionIds.value.includes(option.id)
  );
});

const toggleOption = (option) => {
  const index = disabledOptionIds.value.indexOf(option.id);

  if (index === -1) {
    disabledOptionIds.value.push(option.id);
  } else {
    disabledOptionIds.value.splice(index, 1);
  }
  emit('update', { id: props.field.id, value: disabledOptionIds.value });
};

watch(
    () => props.modelValue,
    (newValue) => {
      disabledOptionIds.value = newValue || [];
    }
);
</script>

<style scoped>
.select-unselect-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  width: 100%;
}

.column {
  height: 100%;
  min-height: 117px;
  border: 1px solid #8b8b8b;
  background: #000;
  border-radius: 2px;
}

h3 {
  margin: 0;
  text-align: center;
  font-weight: normal;
  font-size: 14px;
}

.option {
  margin: 5px;
  cursor: pointer;
  text-align: left;
}

.option.disabled {
  cursor: not-allowed;
}
</style>
