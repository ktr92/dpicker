<template>
  <div class="box">
 
     
      
      <date-picker  
        :disabled-date="disabledBefore" 
        @calendar-change='handleChange' 
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
                  Сегодня {{ todayvalue  }}- {{ tomorrowvalue }} {{ tomorrowmonth }} 
                </button>
                <button class="mx-btn mx-btn-text" @click="selectTomorrow(emit)">
                  Завтра {{ tomorrowvalue  }}  - {{ daftertomorrowvalue }} {{ daftertomorrowmonth }}
                </button>
                <button class="mx-btn mx-btn-text" @click="selectWeekend(emit)">
                  Выходные {{ fstweekvalue  }} - {{ sndweekvalue }} {{ sndweekmonth }}
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
        <template #header>
          <div>
          <div class="sc-modal-header onlymobile"><span class="icon-app-arrow-left"></span><span class="sc-modal-title">Фильтры</span><div class="sc-modal-side-action"><div data-v-1166afda="" class="main-datepicker-reset" @click='clearDates(emit)' style="">Сбросить даты</div></div></div>
          <div class='dpicker__header' v-if='!isDatesNull'>
            <div>
             
              <div  class="sc-datepickerext-wrapper-header"><div  class="sc-datepickerext-wrapper-header--tooltip">Выберите даты поездки, чтобы  цены</div></div>
            </div>
            <div>
             
            </div>
          </div>
        </div>
          
        </template>
      </date-picker>
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
      date: null,
      today: new Date(),
  };
  },
  computed: {
    firstdate() {
      return this.value3[0]
    }, 
    seconddate() {
      return this.value3[1]
    },
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
    },
    todayvalue() {
      return moment(this.today).format('DD')
    },
    todaymonth() {
      return moment(this.today).format('MMM')
    },
  
    tomorrowvalue() {
      return moment(this.today.setTime(this.today.getTime() + 1 * 24 * 3600 * 1000)).format('DD')
    },
    tomorrowmonth() {
      return moment(this.today.setTime(this.today.getTime() + 1 * 24 * 3600 * 1000)).format('MMM')
    },
    daftertomorrowvalue() {
      return moment(this.today.setTime(this.today.getTime() + 2 * 24 * 3600 * 1000)).format('DD')
    },
    daftertomorrowmonth() {
      return moment(this.today.setTime(this.today.getTime() + 2 * 24 * 3600 * 1000)).format('MMM')
    },
    fstweekvalue() {
      return moment(this.getNextSunday()).format('DD')
    },
    sndweekvalue() {
      return moment(this.getNextSunday().getTime() + 1 * 24 * 3600 * 1000).format('DD')
    },
    fstweekmonth() {
      return moment(this.getNextSunday()).format('MMM')
    },
    sndweekmonth() {
      return moment(this.getNextSunday().getTime() + 1 * 24 * 3600 * 1000).format('MMM')
    },
    isDatesNull() {
     
      return this.value3[1]
    },
  },
  watch: {
    date(newvalue, oldvalue) {

      console.log(newvalue, oldvalue)
    },
    
  },
  beforeMount() {
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
     monthDiff(d1, d2) {
      var months;
      months = (d2.getFullYear() - d1.getFullYear()) * 12;
      months -= d1.getMonth();
      months += d2.getMonth();
      return months <= 0 ? 0 : months;
  },
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
  addOneMonth(date) {
    var o = new Date(+date);
    date.setMonth(date.getMonth() + 1);

    if (date.getDate() != o.getDate()) {
    date.setDate(0);
    }
    console.log(date)
    return date
  },
 
  },
};
</script>

