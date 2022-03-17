<script setup lang="ts">
import NumberInput from "./components/number-input.vue";
import { reactive } from "vue";

const torque = reactive({
  max: 2.15,
  precision: 2,
  step: 0.01,
});

const spec = reactive({
  maxUpper: 2.88,
});
</script>

<template>
  <div class="text-center selection:bg-green-100">
    <NumberInput
      :name="'maxTorque'"
      v-model="torque.max"
      :control="true"
      :min="0"
      :precision="torque.precision"
      :max="spec.maxUpper"
      :step="torque.step"
      class="w-44"
    />
  </div>
</template>

<style></style>
