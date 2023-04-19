<template>
  <q-page padding class="fit row">
    <div class="blockquote q-mb-xl">
      <h3 class="text-h6">
        La calculadora sigue las reglas de precedencia de operadores. <br />The
        calculator follows the operator precedence rules.
      </h3>
    </div>
    <div class="full-width column">
      <div class="col-12 col-md-8">
        <q-card>
          <q-card-section class="bg-purple-6 text-white">
            <div class="text-h6">Calculadora</div>
          </q-card-section>
          <q-card-section>
            <div class="text-h5 text-grey-5 text-right">
              {{ accumulator + ' ' + currentValue }}
            </div>
            <div class="text-h3 text-right">{{ result }}</div>
          </q-card-section>
          <q-card-section class="bg-grey-4">
            <div class="row q-col-gutter-sm">
              <div class="col-3" v-for="(btn, i) in buttons" :key="i">
                <q-btn
                  class="full-width text-h6"
                  :color="NotANumber(btn) ? 'indigo' : 'grey-2'"
                  :text-color="NotANumber(btn) ? 'white' : 'grey-8'"
                  @click="btnAction(btn)"
                  :disable="btn === '.' && currentValue === ''"
                >
                  {{ btn }}
                </q-btn>
              </div>

              <div class="col-6">
                <q-btn
                  class="full-width text-h6"
                  color="indigo"
                  @click="btnReset"
                >
                  RESET
                </q-btn>
              </div>
              <div class="col-6">
                <q-btn
                  class="full-width text-h6"
                  color="orange"
                  @click="btnResult"
                >
                  =
                </q-btn>
              </div>
            </div>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { MathCollection, MathNumericType, evaluate, round } from 'mathjs';

const buttons = [7, 8, 9, '%', 4, 5, 6, '+', 1, 2, 3, '-', '.', 0, '/', '*'];
const NotANumber = (btn: number | string) => {
  if (btn === '.' && currentValue.value === '') {
    return false;
  }
  return isNaN(Number(btn));
};
const currentValue = ref<string>('');
const accumulator = ref<string>('');
const result = ref<string | MathNumericType | MathCollection>('');
const opClick = ref<boolean>(true);

const btnAction = (value: number | string) => {
  if (!NotANumber(value)) {
    if (opClick.value) {
      currentValue.value = '';
      opClick.value = false;
    }

    currentValue.value = `${currentValue.value}${value}`;
  } else {
    executeOp(value);
  }
};

const executeOp = (value: number | string) => {
  if (value === '.') {
    if (currentValue.value.indexOf('.') === -1) {
      if (opClick.value) {
        currentValue.value = '';
        opClick.value = false;
      }
      currentValue.value = `${currentValue.value}${value}`;
    }

    return;
  }
  if (value === '%') {
    {
      if (currentValue.value !== '') {
        currentValue.value = `${parseFloat(currentValue.value) / 100}`;
      }
    }
    return;
  }

  addOp(value);
};

const addOp = (value: number | string) => {
  if (!opClick.value) {
    accumulator.value += ` ${currentValue.value} ${value}`;
    currentValue.value = '';
    opClick.value = true;
  }
};

const btnReset = () => {
  currentValue.value = '';
  accumulator.value = '';
  result.value = '';
  opClick.value = true;
};

const btnResult = () => {
  if (!opClick.value && !currentValue.value.endsWith('.')) {
    result.value = evaluate(accumulator.value + currentValue.value);
    result.value = round(result.value as number, 3);
  }
};
</script>

<style lang="scss" scoped>
.text-h5 {
  height: 23px;
}
.text-h3 {
  height: 50px;
}

.blockquote {
  max-width: 800px;
  font-style: italic;
  color: #0b4327;
  background-color: #f8fffe;
  border: 1px solid #770992;
  padding: 25px;
  border-left-width: 10px;
  height: fit-content;
}
</style>
