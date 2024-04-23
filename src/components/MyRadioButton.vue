<script setup>
const { modelValue, id, value } = defineProps({
  modelValue: String | Boolean,
  id: { type: String, required: true },
  name: String,
  value: String | Number,
  checked: Boolean,
});
</script>

<template>
  <input
    type="radio"
    class="input"
    :value="modelValue"
    @change="$emit('update:modelValue', $event.target.value)"
    v-bind="$props"
  />
  <label :for="id" />
</template>

<style scoped lang="css">
label {
  display: block;
  position: relative;
  width: 20px;
  height: 20px;
  border-radius: 5px;
  background-color: var(--secondary);
}
label:hover {
  cursor: pointer;
}

.input:checked + label::after {
  content: "";
  position: absolute;
  inset: 5px;
  border-radius: 2px;
  background-color: var(--primary);
}
.input {
  display: none;
}
</style>
