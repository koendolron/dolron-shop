<script setup>
const props = defineProps({
  modelValue: { type: String, required: true },
  countryCode: { type: String, required: true, default: 'NL' },
});

const emit = defineEmits(['update:modelValue']);

const states = ref([]);

function select(evt) {
  emit('update:modelValue', evt.target.value);
}

async function updateState() {
  try {
    const { countryStates } = await GqlGetStates({ country: props.countryCode });
    states.value = countryStates;
  } catch (error) {
    console.log(error);
  }
}

watch(
  () => props.countryCode,
  () => {
    updateState();
  },
);

onMounted(() => {
  updateState();
});
</script>

<template>
  <select @change="select" v-if="states.length">
    <option v-for="state in states" :key="state.code" :value="state.code" :selected="state.code === props.modelValue">
      {{ state.name }}
    </option>
  </select>
  <input v-else type="text" @change="select" placeholder="State" />
</template>

<style scoped lang="postcss">
select {
  height: 42px;
}
</style>
