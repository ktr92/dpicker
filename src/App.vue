<template>
    <div class="box">
      <section>
      <p>footer slot</p>
      <date-picker v-model:value="value3" range placeholder="Select date range">
        <template #footer="{ emit }">
          <div style="text-align: left">
            <button class="mx-btn mx-btn-text" @click="selectNextThreeDay(emit)">
              Ближайшие 3 дня
            </button>
            <button class="mx-btn mx-btn-text" @click="selectTomorrow(emit)">
              Завтра
            </button>
          </div>
        </template>
      </date-picker>
    </section>
  
  </div>
</template>

<script>
import DatePicker from 'vue-datepicker-next';
  import 'vue-datepicker-next/index.css';

  export default {
    components: { DatePicker },
    data() {
      return {
        value3: null,
    };
    },
    methods: {
    selectNextThreeDay(emit) {
      const start = new Date();
      const end = new Date();
      end.setTime(end.getTime() + 3 * 24 * 3600 * 1000);
      const date = [start, end];
      emit(date);
    },
    selectTomorrow(emit) {
      const start = new Date();
      const end = new Date();
      start.setTime(end.getTime() + 1 * 24 * 3600 * 1000);
      end.setTime(end.getTime() + 2 * 24 * 3600 * 1000);
      const date = [start, end];
      emit(date);
    },
  },
  };
</script>

<style>

</style>
