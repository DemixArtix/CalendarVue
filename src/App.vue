<template>
  <div id="app">
    <Calendar :year="year" :month="month" :number="number" :lang="curLang.key" @getSelectedDate="selectDate"/>
    <div class="input-date">
      <h5>Введите дату</h5>
      <input
          type="text"
          ref="input"
          class="form-control"
          v-model.trim="input"
          @keyup.delete="replaceCursor"
      >
      <h5>Выберите язык</h5>
      <div class="lang form-select rounded-0" @click="showList = !showList">
        <span class="lang__current">{{curLang.desc}}</span>
        <div class="lang__list list-group" v-if="showList" @click.stop>
          <div class="lang__item list-group-item" v-if="lang.key !== curLang.key"  v-for="lang of langList" @click="setLang(lang)">{{lang.desc}}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import Calendar from "./components/Calendar";
export default {
  name: 'App',
  components: {
    Calendar
  },
  data: () => ({
    input: '',
    regExp: /(\d{2})-(\d{2})-(\d{4})/,
    number: null,
    month: null,
    year: null,
    curLang: {key: 'en', desc: 'English'},
    langList: [{key: 'en', desc: 'English'}, {key: 'ru', desc: 'Русский'}],
    showList: false,
  }),
  methods: {
    setLang(lang) {
      this.curLang = lang;
      this.showList = false;
    },
    replaceCursor() {
      if(this.input[this.input.length - 1] === '-') {
        this.$refs.input.selectionEnd = this.input.length - 1;
        const strToArr = this.input.split('');
        strToArr.splice(this.input.length - 1, 1);
        this.input = strToArr.join('');
      }
    },
    addZero(data) {
      let withZero = [];
      withZero.push(data.toString());
      withZero.unshift('0');
      withZero = withZero.join('');
      return withZero;
    },
    selectDate({year, month, day}) {
      this.year = +year;
      this.month = +month;
      this.number = +day;
      let formattedDay = day.toString();
      let formattedMonth = month.toString();
      if(day.toString().length === 1) {
        formattedDay = this.addZero(day);
        console.log(formattedDay)
      }
      if(month.toString().length === 1) {
        formattedMonth = this.addZero(month);
      }
      this.input = `${formattedDay}-${formattedMonth}-${year}`;
    }
  },
  watch: {
    input: function () {
      const strToArr = this.input.replace(/\D-/g, '').split('');
      if(strToArr.length === 2 && strToArr[1] !== '-') {
        strToArr.splice(2, 0, '-')
      }
      if(strToArr.length === 5 && strToArr[4] !== '-') {
        strToArr.splice(5, 0, '-')
      }
      this.input = strToArr.slice(0, 10).join('');
      const result = this.input.match(this.regExp);
      if(result !== null) {
        this.number = +result[1];
        this.month = +result[2];
        this.year = +result[3];
      } else {
        this.number = this.month = this.year = '';
      }
    }
  }
}
</script>

<style lang="scss">
#app {
  width: 100%;
  display: flex;
  justify-content: center;
  box-sizing: border-box;
  .input-date {
    margin: 10px;
    .form-control {
      border-radius: 0;
    }
  }
  .lang {
    border: 1px solid lighten(#000, 80%);
    position: relative;
    &__ {
      &list{
        position: absolute;
        top: calc(100% + 5px);
        left: 0;
        width: 100%;
        border-radius: 0;

      }
    }
  }

}
</style>
