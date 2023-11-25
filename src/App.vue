<!-- eslint-disable no-unused-vars -->
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
          @calendar-change='handleChange' 
          @close='handeClose' 
          v-model:value="value3" 
          range 
          placeholder="Select date range" 
          v-model:open="open"
          @change="handleChange"
          >
          <template #footer="{ emit }">
            <div class='dpicker__footer'>
              <div class='dpicker__fnav'>
                <div style="text-align: left">
                  <button class="mx-btn mx-btn-text" @click="selectToday(emit)">
                    Сегодня <span class='onlydesktop'> {{ todayvalue  }} - {{ tomorrowvalue }} {{ tomorrowmonth }} </span>
                  </button>
                  <button class="mx-btn mx-btn-text" @click="selectTomorrow(emit)">
                    Завтра <span class='onlydesktop'> {{ tomorrowvalue  }}  - {{ daftertomorrowvalue }} {{ daftertomorrowmonth }} </span>
                  </button>
                  <button class="mx-btn mx-btn-text" @click="selectWeekend(emit)">
                    Выходные <span class='onlydesktop'>  {{ fstweekvalue  }} - {{ sndweekvalue }} {{ sndweekmonth }} </span>
                  </button>
                </div>
              </div>
              <div>
                <button class="mx-btn mx-btn-text clearfooter" @click="clearDates(emit)">
                    Сбросить даты
                  </button>
              </div>
            </div>
            <div>
              <div data-v-1166afda="" class="main-datepicker-calendar--footer" style=""><button @click='open = false' data-v-1166afda="" class="button button_size_md button_dark-gray button_w-100">Сохранить</button></div>
            </div>
            
          </template>
          <template #header>
            <div class="sc-modal-header onlymobile">
              <div class='dpcikerclose' @click='open = false'>Назад<span class="icon-app-arrow-left"></span></div>
            
              <span class="sc-modal-title"></span>
              <div class="sc-modal-side-action">
              
              <div data-v-1166afda="" class="main-datepicker-reset" @click='clearDates(emit)' style="">Сбросить даты</div></div></div>
                <div class='dpicker__header' v-if='!isDatesNull'>
                  <div>
                    <div  class="sc-datepickerext-wrapper-header"><div  class="sc-datepickerext-wrapper-header--tooltip">Выберите даты поездки, чтобы увидеть цены</div></div>
                  </div>
                  <div>
                  
                  </div>
                </div>
                <div v-else>
                <div class='dpicker__nav' >
                  <div  class='dpicker_values'>
                  {{ startdate }} - {{ enddate }} /  <span v-if='daysNumber'>{{ daysNumber }} {{ daysNumber === 1 ? " сутки" : " суток"  }}</span>
                </div>
                  <div class='sc-modal-ok onlydesktop' @click='open = false'>Сохранить</div>
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
        date: null,
        today: new Date(),
        lastelement: false,
        ddnumber: document.querySelectorAll('td.cell.in-range:not(.not-current-month)').length + 1
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
        return sdate ?  moment(sdate).format('DD MMM, dd') : ""
      },
      enddate() {
        let edate = this.value3[1]
        return edate ?  moment(edate).format('DD MMM, dd') : ""
      },
      interval() {
       
        return this.intervalvalue
      },
      todayvalue() {
        return moment(new Date()).format('DD')
      },
      tomorrowday() {
        return new Date().setTime(new Date().getTime() + 1 * 24 * 3600 * 1000)
      },
      daftertomorrowday() {
        return new Date().setTime(new Date().getTime() + 1 * 24 * 3600 * 1000)
      },
      todaymonth() {
        return moment(new Date()).format('MMM')
      },
    
      tomorrowvalue() {
        return moment(this.tomorrowday).format('DD')
      },
      tomorrowmonth() {
        return moment(new Date().setTime(new Date().getTime() + 1 * 24 * 3600 * 1000)).format('MMM')
      },
      daftertomorrowvalue() {
        return moment(new Date().setTime(new Date().getTime() + 2 * 24 * 3600 * 1000)).format('DD')
      },
      daftertomorrowmonth() {
        return moment(new Date().setTime(new Date().getTime() + 2 * 24 * 3600 * 1000)).format('MMM')
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
       
        return this.value3[0]
      },
      daysNumber() {
       return this.value3[1] ? moment(this.value3[1]).diff(moment(this.value3[0]), 'days') : null
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
      document.addEventListener("click", function(e){
        _this.checklasslist(e)
      })
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
      checklasslist(e) {
        if ( e.target) {
          if (e.target.closest('.cell') || e.target.closest('.mx-btn')) {
            this.lastelement = true
          }
          else {
            if (e.target.classList.contains('cell') || e.target.classList.contains('mx-btn')) {
              this.lastelement = true
            } else {
              this.lastelement = false
            }
          }
            
          }
      },
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
    
        if (this.lastelement) {
          this.open = true  
          this.lastelement = false
        }
        
        const tooltip = document.querySelector('.tooltip')
              tooltip.style.top = -999999999 + 'px'
              tooltip.style.left = -999999999 + 'px'
      },
      clearDates() {
        this.value3 = [null, null]
        document.querySelectorAll('.cell').forEach(item => {
          item.classList.remove('active')
          item.classList.remove('in-range')
        })
      },
     
      disabledBefore(date) {
        return date < new Date(new Date().setHours(0, 0, 0, 0));
    },
    addOneMonth(date) {
      var o = new Date(+date);
      date.setMonth(date.getMonth() + 1);

      // If have rolled over an extra month, set to last
      // day of previous month
      if (date.getDate() != o.getDate()) {
      date.setDate(0);
      }
      console.log(date)
      return date
    },
    // eslint-disable-next-line no-unused-vars
    handleChange(value, type) {

      this.open = true
      this.ddnumber = document.querySelectorAll('td.cell.in-range:not(.not-current-month)').length + 1

      document.getElementById('datein').value = this.value3[0]
      document.getElementById('dateout').value = this.value3[1]
      // поменять местами кнопки
   /*   
     if (this.monthDiff(date[0], date[1]) > 1) {
      date[0] = this.addOneMonth(date[0])
     } */
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
  width: 340px;
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
button.mx-btn.mx-btn-text.mx-btn-icon-left {
    position: absolute;
    left: 20px;
}
button.mx-btn.mx-btn-text.mx-btn-icon-right {
    position: absolute;
    right: 20px;
}

.mx-calendar.mx-calendar-panel-date:first-child button.mx-btn.mx-btn-text.mx-btn-icon-left {
display: none;
}
.mx-calendar.mx-calendar-panel-date:last-child button.mx-btn.mx-btn-text.mx-btn-icon-right {
  display: none;
}
.sc-datepickerext-wrapper-header {
  background: #fff6c4;
    color: #000;
    overflow: hidden;
    padding: 9px 20px;
    align-items: center;
    color: #000;
    display: flex;
    font-size: 14px;
    font-weight: 500;
    line-height: 17px;
    font-weight: bold;
}
.dpicker__footer button.mx-btn.mx-btn-text {
  align-items: center;
    background-color: #f1f3fb;
    border: none;
    border-radius: 50px;
    cursor: pointer;
    margin-right: 4px;
    font-size: 12px;
    font-weight: 500;
    height: 30px;
    justify-content: center;
    letter-spacing: .2px;
    line-height: 16px;
    margin-left: 0;
    margin-bottom: 2px;
    padding: 0 10px;
}

.mx-calendar-header-label .mx-btn-text {
  color: #000;
  font-size: 16px;
    font-weight: 600;
    line-height: 24px;
    margin-bottom: 10px;
    font-weight: bold;
    text-transform: capitalize;
}


</style>
