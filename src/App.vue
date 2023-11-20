<template>
    <div class="box">
      <section>
        <div class="tooltip">{{ interval }} {{ interval === 1 ? " сутки" : " суток"}}</div><!-- /.tooltip -->

      <div class='dpicker__wrapper'>
        <input type="text" @focus='open = !open' v-model='startdate'>
        <input type="text" @focus='open = !open' v-model='enddate'>
        <date-picker @close='handeClose' v-model:value="value3" range placeholder="Select date range" v-model:open="open">
          <template #footer="{ emit }">
            <div style="text-align: left">
              <button class="mx-btn mx-btn-text" @click="selectToday(emit)">
                Сегодня
              </button>
              <button class="mx-btn mx-btn-text" @click="selectTomorrow(emit)">
                Завтра
              </button>
              <button class="mx-btn mx-btn-text" @click="selectWeekend(emit)">
                Выходные
              </button>
            </div>
          </template>
        </date-picker>
      </div>
      
    </section>
  
  </div>
</template>

<script>
import moment from 'moment'

import 'vue-datepicker-next/locale/ru';

import DatePicker from 'vue-datepicker-next';
  import 'vue-datepicker-next/index.css';

  export default {
    components: { DatePicker },
    data() {
      return {
        value3: [null, null],
        intervalvalue: 0,
        open: false,
        langString: 'ru',
    };
    },
    computed: {
      startdate() {
        let sdate = this.value3[0]
        return sdate ?  moment(sdate).format('DD MMMM, dd') : ""
      },
      enddate() {
        let edate = this.value3[1]
        return edate ?  moment(edate).format('DD MMMM, dd') : ""
      },
      interval() {
       
        return this.intervalvalue
      }
    },
    mounted() {
      moment.locale('ru');
      let _this = this
      document.addEventListener("mouseover", function(e){
        let int = null
        if (int) {
          clearTimeout(int)
        }
        const target = e.target.closest(".cell") ? e.target.closest(".cell") : e.target
        if(target ){
          int = setTimeout(() => {
            _this.intervalvalue = document.querySelectorAll('td.cell.hover-in-range:not(.not-current-month)').length + 1; 

            let active = document.querySelectorAll('.cell.active:not(.not-current-month)').length; 

            if (_this.intervalvalue > 0 && active === 1) {
              var x = target.getBoundingClientRect().left 
              var y = target.getBoundingClientRect().top 
            } else {
               x = -100
               y = -100
            }
            const tooltip = document.querySelector('.tooltip')
              tooltip.style.top = y + 'px'
              tooltip.style.left = x + 'px'
          }, 100)
        }  
      });
    },
    methods: {
       getNextSunday(date = new Date()) {
        const dateCopy = new Date(date.getTime());

        const nextSunday = new Date(
          dateCopy.setDate(
            dateCopy.getDate() + ((7 - dateCopy.getDay() + 6) % 7 || 7),
          ),
        );

        return nextSunday;
      },
      selectToday(emit) {
        const start = new Date();
        const end = new Date();
        end.setTime(end.getTime() + 1 * 24 * 3600 * 1000);
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
      selectWeekend(emit) {
        const start = this.nextSunday
        const end = new Date();
        start.setTime(end.getTime());
        end.setTime(end.getTime() + 1 * 24 * 3600 * 1000);
        const date = [start, end];
        emit(date);
      },
      handeClose() {
        const tooltip = document.querySelector('.tooltip')
              tooltip.style.top = -999999999 + 'px'
              tooltip.style.left = -999999999 + 'px'
      }
     /*  handleOpen() {
       
        this.cells = document.querySelectorAll("td.cell")
        this.cells.forEach(cell => cell.addEventListener('mouseover', function() {
          this.intervalvalue = document.querySelectorAll('.hover-in-range').length;
        }))
      } */
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

  .tooltip {
  background-color: #545454;
  -webkit-border-radius: 2px;
  line-height: 1.1;
  border-radius: 2px;
  text-align: left;
  color: #fff;
  font-size: 11px;
  padding: 6px 10px;
  margin-bottom: 10px;
  position: relative;
  position: absolute;
  left: -999999999px;
  z-index: 9999;
  top: -999999999px;
  width: 51px;
  white-space: normal;
  transform: translateX(-22px) translateY(-20px);
}
/* .tooltip::after {
  content: "";
  border-left: 4px solid transparent;
  border-right: 4px solid transparent;
  border-top: 4px solid #545454;
  bottom: -4px;
  left: 50%;
  margin-left: -4px;
  position: absolute;
} */
</style>
