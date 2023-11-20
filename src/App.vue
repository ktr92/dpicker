<template>
    <div class="box">
      <section>
        <div class="tooltip">{{ interval }} {{ interval === 1 ? " сутки" : " суток"}}</div><!-- /.tooltip -->

      <div class='dpicker__wrapper'>
       <!--  <input type="text" @focus='open = !open' v-model='startdate'>
        <input type="text" @focus='open = !open' v-model='enddate'> -->
        <div class='search_datepicker'>
          <div @click='open = !open' data-cy="checkIn" class="search-widget-field--occupied__in"><div class="title">Заезд</div><div class="date">{{ startdate ? startdate : "Когда"}}</div></div>

<div @click='open = !open' data-cy="checkOut" class="search-widget-field--occupied__out"><div class="title">Отъезд</div><div class="date">{{ enddate ? enddate : "Когда" }}</div></div>
        </div>

       
        
        <date-picker  
          :disabled-date="disabledBefore" 
          @close='handeClose' 
          v-model:value="value3" 
          range 
          placeholder="Select date range" 
          v-model:open="open">
          <template #footer="{ emit }">
            <div class='dpicker__footer'>
              <div>
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
              </div>
              <div>
                <button class="mx-btn mx-btn-text" @click="clearDates(emit)">
                    Сбросить даты
                  </button>
              </div>
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
        const start = this.getNextSunday()
        const end = new Date();
        end.setTime(start.getTime() + 1 * 24 * 3600 * 1000);
        const date = [start, end];
        emit(date);
      },
      handeClose() {
        const tooltip = document.querySelector('.tooltip')
              tooltip.style.top = -999999999 + 'px'
              tooltip.style.left = -999999999 + 'px'
      },
      clearDates() {
        this.value3 = [null, null]
      },
      disabledBefore(date) {
        return date < new Date(new Date().setHours(0, 0, 0, 0));
    },
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
.dpicker__footer{
  display: flex;
  align-items: center;
  justify-content: space-between;
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


.search-widget-field--occupied__in .title, .search-widget-field--occupied__out .title {
    color: #5a5d63;
    font-size: 12px;
    letter-spacing: .2px;
    line-height: 16px;
}

.search-widget-field--occupied__in .date, .search-widget-field--occupied__out .date {
    font-size: 14px;
    font-weight: 500;
    line-height: 20px;
    font-weight: bold;
}
.search-widget-field--occupied__in, .search-widget-field--occupied__out {
    cursor: pointer;
    display: flex;
    flex-direction: column;
    height: 100%;
    justify-content: center;
    padding: 0 12px;
    position: relative;
    transition: background-color .2s;
    white-space: nowrap;
    width: 50%;
}
.search_datepicker {
  display: flex;
  align-items: center;
  width: 232px;
  margin-left: auto;
  margin-right: auto;
}
.mx-datepicker-popup {
  margin-left: auto;
  margin-right: auto;
  right: 0;
  width: fit-content;
}
.mx-calendar-content .cell {
  align-items: center;
   /*  border-radius: 100%; */
    height: 40px;
    justify-content: center;
    position: relative;
    min-width: 40px;
    font-size: 15px;
    font-weight: bold;
    border-bottom: 4px solid #fff;
}
.mx-date-row {
  
}
.mx-calendar {
  width: 320px;
  padding: 20px;
}
.mx-calendar-content {
  height: unset;
}
/* td.cell.in-range {
  border-radius: 0;
}
td.cell.hover-in-range {
  border-radius: 0;
} */
td.cell.not-current-month {
  opacity: 0;
}
body {
  
  font-family: sans-serif;
}
button.mx-btn.mx-btn-text.mx-btn-icon-double-left, button.mx-btn.mx-btn-text.mx-btn-icon-double-right {
display: none;
}
</style>
