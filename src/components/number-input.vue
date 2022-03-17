<template>
  <div class="flex justify-between rounded-lg border-2 border-gray-400">
    <button
      v-if="control"
      @click="decrement"
      :disabled="stand <= min"
      class="mb-1 mt-0.5 ml-0.5 rounded-l-lg px-3 text-center text-white"
      :class="[
        stand <= min
          ? 'shadow-disabled pointer-events-none bg-gray-200'
          : ' shadow-dark bg-gray-400 hover:bg-gray-600 active:bg-gray-600 active:shadow-none',
      ]"
      tabindex="-1"
    >
      -
    </button>
    <input
      id="numberInput"
      class="w-full rounded-lg px-2 text-center focus:outline-none"
      ref="input"
      :name="name"
      :value="modelValue"
      type="text"
      :max="max"
      :min="min"
      :precision="precision"
      :step="step"
      @input="handleInput"
    />
    <button
      v-if="control"
      @click="increment"
      class="shadow-dark mb-1 mt-0.5 mr-0.5 rounded-r-lg bg-gray-400 px-3 text-center text-white hover:bg-gray-600 active:bg-gray-600 active:shadow-none"
      :class="[
        stand >= max
          ? 'shadow-disabled pointer-events-none bg-gray-200'
          : ' shadow-dark bg-gray-400 hover:bg-gray-600 active:bg-gray-600 active:shadow-none',
      ]"
      tabindex="-1"
    >
      +
    </button>
  </div>
</template>

<script setup lang="ts">
import { defineProps, reactive, defineEmits, watch, ref, onUpdated } from "vue";

const props = defineProps({
  name: String,
  modelValue: { type: Number, default: 0 },
  max: {
    type: Number,
    default: Infinity,
  },
  min: {
    type: Number,
    default: -Infinity,
  },
  precision: {
    type: Number,
    validator(val: Number) {
      return val >= 0 && Number.isInteger(val);
    },
    default: 0,
  },
  step: {
    type: Number,
    default: 1,
  },
  control: {
    type: Boolean,
    default: false,
  },
  change: {
    type: Boolean,
    default: true,
  },
  isNegtive: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits(["update:modelValue"]);

const input = document.getElementById("numberInput") as HTMLInputElement;

const state = reactive({
  numericValue: 0,
  handler: Function,
});

const same = ref(props.modelValue);

const stand = onStand();

function onStand() {
  let value = 0;
  if (typeof props.modelValue === "number") {
    value = props.modelValue;
  }
  return ref(value);
}

const step = props.step;

function decrement() {
  let newNumber = toPrecision((stand.value -= step), props.precision);
  if (newNumber >= props.max) {
    newNumber = props.max;
  }
  if (newNumber <= props.min) {
    newNumber = props.min;
  }
  emit("update:modelValue", newNumber);
}

function increment() {
  let newNumber = toPrecision((stand.value += step), props.precision);
  if (newNumber >= props.max) {
    newNumber = props.max;
  }
  if (newNumber <= props.min) {
    newNumber = props.min;
  }
  emit("update:modelValue", newNumber);
}

function handleInput(e: Event) {
  const val = (e.target as HTMLInputElement).value;

  updateValue(toNumber(val));
}

function toNumber(val: string) {
  let num = parseFloat(val);
  return num;
}

function updateValue(val: number) {
  let temp = toPrecision(val, props.precision);
  console.log(temp);

  if (temp >= props.max) {
    temp = props.max;
  }
  if (temp <= props.min) {
    temp = props.min;
  }

  // state.numericValue = temp;
  let newVal = temp;
  if (Number.isNaN(temp)) {
    newVal = 0;
  }
  console.log("new", newVal);
  emit("update:modelValue", newVal);
}

function toPrecision(val: number, precision: number) {
  const newPrecision = precision + 1;
  return newPrecision !== undefined
    ? parseFloat(val.toFixed(newPrecision).slice(0, -1))
    : val;
}

function negativeValidate(val: string) {
  return /\-?(0)?(\.)?(0)/g.test(val);
}

onUpdated(() => {
  console.log(props.modelValue);
});
</script>
