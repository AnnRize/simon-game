<script>
export default {
  inheritAttrs: false,
};
</script>

<script setup>
const model = defineModel();
const { id } = defineProps({
  id: { type: String, required: true },
});
</script>

<template>
  <input type="radio" class="input" :id="id" v-model="model" v-bind="$attrs" />
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
