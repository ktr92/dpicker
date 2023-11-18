<template>
    <div class="box">
      <section>
      <p>footer slot</p>
      <div class='dpicker__wrapper'>
        <input type="text" @focus='open = !open' v-model='value3'>
        <input type="text" @focus='open = !open' v-model='value3'>
        <date-picker v-model:value="value3" range placeholder="Select date range" v-model:open="open">
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
      </div>
      
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
        value3: [null, null],
        time0: null,
        time1: null,
        time2: null,
        time3: null,
        open: false,
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
.mx-input-wrapper {
  display: none;
  width: 0;
}
.mx-datepicker-popup {
  left: 0 !important;
  margin-top: 10px;
}

  .dpicker__wrapper {
    position: relative;
  }
</style>
