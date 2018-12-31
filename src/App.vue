<template>
  <div id="app">
    <div class="container">
      <h1>Mega Challenge Calculator</h1>
      <div class="row">
        <div class="col form-inline start-sum">
          <label for="start-sum-input">Начальная сумма:</label>
          <input v-model="sumStart"
                 @input="checkSumStart"
                 id="start-sum-input"
                 type="text"
                 class="form-control"/>
        </div>
      </div>
      <div class="row">
        <div class="offset-lg-2 col col-md-6 col-lg-4">
          <div class="tip">Дата начала: {{formatDayStart}}</div>
          <flat-pickr v-model="dateStart"
                      :config="config"
                      class="form-control"/>
        </div>
        <div class="col col-md-6 col-lg-4">
          <div class="tip">Дата окончания: {{formatDayEnd}}</div>
          <flat-pickr v-model="dateEnd"
                      :config="config"
                      class="form-control"/>
        </div>
      </div>

      <div class="row">
        <div class="col">
          <div class="result">
            <strong>Результат:</strong> за {{daysCount}} дней вы накопите <strong>{{sum}}&nbsp;руб.</strong>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import flatPickr from 'vue-flatpickr-component';
  import { Russian } from 'flatpickr/dist/l10n/ru.js';
  import 'flatpickr/dist/flatpickr.css';

  const months = [
    'января', 'февраля', 'марта', 'апреля', 'мая', 'июня',
    'июля', 'августа', 'сентября', 'октября', 'ноября', 'декабря'
  ];
  const formatNow = () => {
    const now = new Date();
    return `${now.getDate()}.${now.getMonth() + 1}.${now.getFullYear()}`
  };

  export default {
    name: 'app',
    data() {
      return {
        sumStart: 100,
        dateStart: formatNow(),
        dateEnd: '31.12.2019',
        config: {
          dateFormat: 'd.m.Y',
          inline: true,
          locale: Russian
        }
      }
    },
    components: {
      flatPickr,
    },
    computed: {
      formatDayStart() {
        const date = this.dateStart.match(/\d+\b/g);
        const month = +date[1] - 1;
        return `${date[0]} ${months[month]} ${date[2]} года`
      },

      formatDayEnd() {
        const date = this.dateEnd.match(/\d+\b/g);
        const month = +date[1] - 1;
        return `${date[0]} ${months[month]} ${date[2]} года`
      },

      daysCount() {
        const startDate = new Date(this.dateStart.replace(/(\d+)\.(\d+)\.(\d+)/, '$2/$1/$3'));
        const endDate = new Date(this.dateEnd.replace(/(\d+)\.(\d+)\.(\d+)/, '$2/$1/$3'));

        return Math.abs(endDate.getTime() - startDate.getTime()) / 1000 / 60 / 60 / 24
      },

      sum() {
        let res = this.sumStart;
        for (let i = 0; i <= this.daysCount; i++) {
          res += i
        }

        return res
      }
    },

    methods: {
      checkSumStart($event) {
        const value = parseInt($event.target.value.trim(), 10);
        this.sumStart = isNaN(value) ? 0 : value
      }
    }
  }
</script>

<style lang="less">
  body {
    min-width: 320px;
  }

  #app {
    font-family: Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    padding: 2rem 0;
  }

  h1 {
    text-align: center;
    margin: 0;
    padding-bottom: 2rem;
  }

  .start-sum {
    justify-content: center;
    margin-bottom: 1.5rem;

    label {
      margin-right: 6px;
    }
  }

  .tip {
    padding: 0.15rem 0;
  }

  .flatpickr-input {
    display: none;
  }

  .flatpickr-calendar {
    box-shadow: none;
    padding-bottom: 0.3rem;
    border-color: #ffffff;
    margin: 0.2rem 0;

    .flatpickr-months {
      padding-bottom: 0.5rem;
    }
  }

  .result {
    text-align: center;
    padding: 2rem 0 1rem;
    color: #569ff7;
  }

  @media (max-width: 767px) {
    .start-sum {
      justify-content: flex-start;

      input {
        width: 200px
      }
    }

    .result {
      text-align: left;
    }
  }
</style>
