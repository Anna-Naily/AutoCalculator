<template>
  <div>
    <div class="main-container">
      <h1 class="heading">Рассчитайте стоимость автомобиля в лизинг</h1>
      <div class="center-block">
        <div class="center-block__element">
          <h3 class="center-block__text">Стоимость автомобиля</h3>
          <div class="center-block__input" :class="{disable: isDisabled}">
            <div class="center-block__bunch">
              <input
                class="center-block__input-value"
                id="input_one"
                type="text"
                onkeyup="this.value = this.value.replace(/[^\d]/g,'');"
                v-model="autoCost"
                :min="1000000"
                :max="6000000"
              />
              <span class="center-block__input-word price">₽</span>
            </div>
            <input
              class="center-block__input-range"
              type="range"
              name="numeral"
              id="numeral"
              :min="1000000"
              :max="6000000"
              v-model="autoCost"
              data-rangeslider
            />
          </div>
        </div>
        <div class="center-block__element">
          <h3 class="center-block__text">Первоначальный взнос</h3>
          <div class="center-block__input" :class="{disable: isDisabled}">
            <div class="center-block__bunch second-block">
              <span class="center-block__input-value">{{getInitialFeeAccount}}₽</span>
              <output class="center-block__input-word percents" name="result">{{initialFee}}%</output>
            </div>
            <input
              class="center-block__input-range"
              type="range"
              name="rate"
              id="rate"
              :min="10"
              :max="60"
              v-model="initialFee"
            />
          </div>
        </div>
        <div class="center-block__element">
          <h3 class="center-block__text">Срок лизинга</h3>
          <div class="center-block__input" :class="{disable: isDisabled}">
            <div class="center-block__bunch">
              <input
                class="center-block__input-value"
                type="text"
                onkeyup="this.value = this.value.replace(/[^\d]/g,'');"
                :min="1"
                :max="60"
                v-model="leaseTerm"
              />
              <span class="center-block__input-word term">мес.</span>
            </div>
            <input
              class="center-block__input-range"
              type="range"
              name="month"
              id="month"
              :min="1"
              :max="60"
              v-model="leaseTerm"
            />
          </div>
        </div>
      </div>
      <div class="bottom-block">
        <div class="bottom-block__element">
          <h3 class="bottom-block__heading">Сумма договора лизинга</h3>
          <span class="bottom-block__sum">{{getSumLease}}&nbsp;₽</span>
        </div>
        <div class="bottom-block__element">
          <h3 class="bottom-block__heading">Ежемесячный платеж от</h3>
          <span class="bottom-block__sum">{{getMonthPayment}}&nbsp;₽</span>
        </div>
        <button class="bottom-block__btn" :disabled="isDisabled" @click="sendData">
          <b-spinner variant="light" key="variant" class="spinner" v-show="isVisible"></b-spinner>Оставить заявку
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AutoCalculator',
  data() {
    return {
      autoCost: 1000000, //Стоимость автомобиля
      initialFee: 10, //Первоначальный взнос в %
      initialFeeAccount: 0, //Первоначальный взнос в руб.
      interestRate: 0.035, //Процентная ставка
      leaseTerm: 1, //Срок лизинга
      sumLease: 0, //Сумма договора лизинга
      monthPayment: 0, //Ежемесячный платеж
      isDisabled: false,
      isVisible: false,
      url: 'https://eoj3r7f3r4ef6v4.m.pipedream.net',
      text: '',
      variants: ['light'],
    };
  },
  methods: {
    postJson(url, data) {
      this.isDisabled = true;
      return fetch(url, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(data),
      })
        .then((result) => result.json())
        .catch((err) => {
          this.setError(err);
        })
        .finally(() => ((this.isDisabled = false), (this.isVisible = false)));
    },
    setError(error) {
      this.text = error;
      console.log('Обнаружена ошибка ' + error);
    },
    sendData() {
      this.isVisible = !this.isVisible;
      const allData = {};
      allData.autoCost = this.autoCost;
      allData.initialFee = this.initialFee;
      allData.initialFeeAccount = this.initialFeeAccount;
      allData.interestRate = this.interestRate;
      allData.leaseTerm = this.leaseTerm;
      allData.sumLease = this.sumLease;
      allData.monthPayment = this.monthPayment;
      this.postJson(this.url, allData);
    },
    toCalculateInitialFee() {
      this.initialFeeAccount = (this.initialFee / 100) * this.autoCost;
      return Math.round(this.initialFeeAccount);
    },
    toCalculateSumLease() {
      this.sumLease =
        this.initialFeeAccount + this.leaseTerm * this.monthPayment;
      return Math.round(this.sumLease);
    },
    toCalculateMonthPayment() {
      this.monthPayment =
        (this.autoCost - this.initialFeeAccount) *
        ((this.interestRate * Math.pow(1 + this.interestRate, this.leaseTerm)) /
          (Math.pow(1 + this.interestRate, this.leaseTerm) - 1));
      return Math.round(this.monthPayment);
    },
  },
  computed: {
    getInitialFeeAccount() {
      return this.toCalculateInitialFee();
    },
    getSumLease() {
      return this.toCalculateSumLease();
    },
    getMonthPayment() {
      return this.toCalculateMonthPayment();
    },
  },
};
</script>

<style lang="scss" scoped>
@import '../assets/style/fullscreen.scss';
@import '../assets/style/1024.scss';
@import '../assets/style/768.scss';
@import '../assets/style/320.scss';
@import '../assets/style/main.scss';
</style>
