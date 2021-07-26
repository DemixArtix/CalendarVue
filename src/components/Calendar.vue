<template lang="pug">
  div(class="calendar")
    table(class="calendar__block")
      thead
        tr(class="calendar__row")
          td(class="calendar__prev-month")
            button(class="btn" @click="prevMonth")
          td(class="calendar__current" colspan="5") {{months[lang][curMonth]}} {{curYear}}
          td(class="calendar__next-month")
            button(class="btn" @click="nextMonth")
        tr(class="calendar__row")
          td(class="calendar__day-of-week" v-for="day in daysOfWeek[lang]") {{day}}
      tbody
        tr(class="calendar__row" v-for="(week, index) of formatCurrentMonth()" :key="index")
          td(class="calendar__day" v-for="(day, index) of week" :key="index")
            button(class="btn" :class="{'active': curDay === day}"  @click="setSelectedDate(day)") {{day}}


</template>

<script>
  export default {
    name: "Calendar",
    data: () => ({
      daysOfWeek: {
        en:  ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
        ru: ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс'],
      },
      months: {
        en: ["January","February","March","April","May","June","July", "August","September","October","November","December"],
        ru: ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Ноябрь', 'Декабрь',]
      },
      curMonth: new Date().getMonth(),
      curYear: new Date().getFullYear(),
      curDay: new Date().getDate(),
    }),
    created() {
      this.formatCurrentMonth();
    },
    props: {
      year: {
        type: Number,
        default: null
      },
      month: {
        type: Number,
        default: null
      },
      number: {
        type: Number,
        default: null
      },
      lang: {
        type: String,
        default: ''
      },
    },
    methods: {
      setSelectedDate(day) {
        this.$emit('getSelectedDate', {
          year: this.curYear,
          month: this.curMonth,
          day
        });
        this.curDay = day;
      },
      daysInMonth(year, month) {
        return new Date(year, month + 1, 0).getDate();
      },
      formatCurrentMonth() {
        const numbersInWeek = [];
        let processedWeek = 0; //index of week in numbersInWeek
        numbersInWeek[processedWeek] = [];
        for(let i = 1; i <= this.daysInMonth(this.curYear, this.curMonth); i++) {
          // console.log(new Date(this.curYear, this.curMonth, i).getDay());
          if (new Date(this.curYear, this.curMonth, i).getDay() === 1) {
            processedWeek++;
            numbersInWeek[processedWeek] = [];
          }
          numbersInWeek[processedWeek].push(i);
        }
        if(numbersInWeek[0].length < 7) {
          const numberOfEmptyCells = 7 - numbersInWeek[0].length;
          for (let i = 1; i <= numberOfEmptyCells; i++) {
            numbersInWeek[0].unshift('')
          }
        }
        return numbersInWeek;
      },
      prevMonth() {
        if(this.curMonth === 0) {
          this.curMonth = 12;
          this.curYear--;
        }
        this.curMonth--;
      },
      nextMonth() {
        if(this.curMonth === 11) {
          this.curMonth = -1;
          this.curYear++;
        }
        this.curMonth++;
      }
    },
    watch: {
      year: function () {
        if(this.year && this.month && this.number) {
          this.curYear = +this.year;
          this.curMonth = +this.month - 1;
          this.curDay = +this.number;
        } else {
          this.curYear = new Date().getFullYear();
          this.curMonth = new Date().getMonth();
          this.curDay = new Date().getDate();
        }
      }
    }

  }
</script>

<style lang="sass">
  .calendar
    .calendar__block
      border: 1px solid lighten(#000, 30%)

      thead
        .calendar__row
          .calendar__day-of-week
            font-size: 13px
      tbody

    .calendar__row
      td
        text-align: center
    .calendar__day
      .btn
        width: 40px
        height: 40px
        cursor: pointer
        border-radius: 0
        padding: 0
        &:hover
          background-color: #78CEE6
          color: #fff
        &:focus
          background-color: #fff
          color: #000
          border: 1px solid #78CEE6
          box-shadow: none
        &.active
          background-color: orange
          color: #fff
          border: none
    .calendar__prev-month,.calendar__next-month
      .btn
        position: relative
        border-radius: 0
        &:before
          position: absolute
          content: ''
          left: 50%
          top: 50%
          transform: translate(-50%, -50%)
          border: 5px solid transparent

    .calendar__prev-month
      .btn
        &:before
          border-right: 10px solid #000

    .calendar__next-month
      .btn
        &:before
          border-left: 10px solid #000


</style>