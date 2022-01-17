<template>
  <div class="shop">
    <div v-if="step === 6" class="thankYou">
      Dziękujemy za zakupy
    </div>
    <div v-if="step !== 6" class="container--left animated__fadein">
      <div class="shirt whitebox">
        <div class="shirt__preview">
          <img class="shirt__background"
               v-if="selected_shirt_side === 0"
               src="@/assets/shirtfront.png"/>
          <img class="shirt__background"
               v-if="selected_shirt_side === 1"
               src="@/assets/shirtback.png"/>
          <div class="shirt__image" v-if="(selected_shirt_side === 0 && data.shirt_front.value)
          || (selected_shirt_side === 1 && data.shirt_rear.value)">
            <img :src="data.image.value"/>
          </div>
        </div>
        <button class="btn btn--rotate" v-on:click="flipShirt()">Obróć koszulkę</button>
      </div>
    </div>
    <div v-if="step !== 6" class="container--right animated__fadein">
      <div class="whitebox priceBox">
        <div class="priceBox__text">Suma zamówienia:</div>
        <div class="priceBox__value"> {{ totalPrice }} zł</div>
      </div>
      <div class="whitebox">
        <h1 class="step__title">{{ steps[step]['title'] }}</h1>
        <section v-if="errors.length > 0">
          <div class="errors" id="example-1">
            <p class="errors__row animated__shake"
               v-for="item in errors" :key="item"> {{ item }} </p>
          </div>
        </section>
        <section class="shirtside animated__fadein" v-if="step === 1">
          <div class="shirtside__row">
            <input class="shirtside__checkbox"
                   type="checkbox" id="checkbox-front" v-model="data.shirt_front.value">
            <label class="shirtside__label" for="checkbox-front">Przód</label>
          </div>
          <div class="shirtside__row">
            <input class="shirtside__checkbox"
                   type="checkbox" id="checkbox-rear" v-model="data.shirt_rear.value">
            <label for="checkbox-rear">Tył</label>
          </div>
        </section>
        <section class="image-editor animated__fadein" v-if="step === 2">
          <div class="mainimg">
            <div class="loader" v-if="loading">
              <img class="loader__icon" src="@/assets/loader.png"/>
            </div>
            <img v-else class="mainimg__preview" :src="data.image.value"/>
          </div>
          <div class="imageTools">
            <div class="imageTools__row">
              <button class="btn btn__edit" v-if="data.image.previous_seed"
                      v-on:click="previousImage">Poprzednie</button>
              <button class="btn btn__edit" v-on:click="randomizeImage">Wylosuj inne</button>
            </div>
            <div class="imageTools__row">
              <input v-model="data.grayscale.value" type="checkbox" id="checkbox-grayscale">
              <label for="checkbox-grayscale">Skala szarości</label>
            </div>
            <div class="imageTools__row">
              <button class="btn btn__edit" v-if="data.blur.value > 0"
                      v-on:click="decreaseBlur()">Blur-</button>
              <button class="btn btn__edit" v-if="data.blur.value < data.blur.max_value"
                      v-on:click="increaseBlur()">Blur+</button>
            </div>
          </div>
        </section>
        <section class="animated__fadein" v-if="step === 3 || step === 5">
          <section>
            <div class="confirm__header">Konfiguracja zamówienia</div>
            <div class="confirm">
              <div class="confirm__text">Wybrana grafika</div>
              <button class="btn btn__confirm" v-if="!data.image.confirmed"
                      v-on:click="confirmData('image')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.image)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Strona nadruku:
                <span class="confirm__data" v-if="data.shirt_front.value">Przód</span>
                <span class="confirm__data"
                      v-if="data.shirt_front.value && data.shirt_rear.value"> + </span>
                <span class="confirm__data" v-if="data.shirt_rear.value">Tył</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.shirt_front.confirmed"
                      v-on:click="confirmShirtSide()">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.shirt_front)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Skala szarości:
                <span class="confirm__data" v-if="data.grayscale.value">Tak</span>
                <span class="confirm__data" v-else>Nie</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.grayscale.confirmed"
                      v-on:click="confirmData('grayscale')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.grayscale)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Rozmycie:
                <span class="confirm__data" v-if="data.blur.value">Tak -
                  {{ data.blur.value }}
                </span>
                <span class="confirm__data" v-else>Nie</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.blur.confirmed"
                      v-on:click="confirmData('blur')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.blur)">Edytuj</button>
            </div>
          </section>
          <section class="animated__fadein" v-if="step === 5">
            <div class="confirm__header">Dane do wysyłki</div>
            <div class="confirm">
              <div class="confirm__text">Imię:
                <span class="confirm__data">{{ data.name.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.name.confirmed"
                      v-on:click="confirmData('name')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.name)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Nazwisko:
                <span class="confirm__data">{{ data.surname.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.surname.confirmed"
                      v-on:click="confirmData('surname')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.surname)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Kod pocztowy:
                <span class="confirm__data">{{ data.postal_code.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.postal_code.confirmed"
                      v-on:click="confirmData('postal_code')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.postal_code)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Miasto:
                <span class="confirm__data">{{ data.city.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.city.confirmed"
                      v-on:click="confirmData('city')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.city)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Ulica:
                <span class="confirm__data">{{ data.street.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.street.confirmed"
                      v-on:click="confirmData('street')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.street)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Numer budynku:
                <span class="confirm__data">{{ data.building.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.building.confirmed"
                      v-on:click="confirmData('building')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.building)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Numer mieszkania:
                <span class="confirm__data">{{ data.flat.value }}</span>
                <span class="confirm__data" v-if="!data.flat.value">Nie dotyczy</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.flat.confirmed"
                      v-on:click="confirmData('flat')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.flat)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">Telefon:
                <span class="confirm__data"> {{ data.phone.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.phone.confirmed"
                      v-on:click="confirmData('phone')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.phone)">Edytuj</button>
            </div>
            <div class="confirm">
              <div class="confirm__text">E-mail:
                <span class="confirm__data"> {{ data.mail.value }}</span>
              </div>
              <button class="btn btn__confirm" v-if="!data.mail.confirmed"
                      v-on:click="confirmData('mail')">Potwierdzam</button>
              <img class="confirm__checkmark" v-else src="@/assets/checkmark.png"/>
              <button class="btn btn__edit" v-on:click="goToStep(data.mail)">Edytuj</button>
            </div>
          </section>
        </section>
        <section class="animated__fadein" v-if="step === 4">
          <div class="form">
            <div class="form__row">
              <div class="form__title">Imię*</div>
              <input type="text" v-model="data.name.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Nazwisko*</div>
              <input type="text" v-model="data.surname.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Miasto*</div>
              <input type="text" v-model="data.city.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Ulica*</div>
              <input type="text" v-model="data.street.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Numer budynku*</div>
              <input type="text" v-model="data.building.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Numer mieszkania</div>
              <input type="text" v-model="data.flat.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Kod pocztowy*</div>
              <input type="text" v-model="data.postal_code.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Numer telefonu</div>
              <input type="text" v-model="data.phone.value" class="form__input">
            </div>
            <div class="form__row">
              <div class="form__title">Adres e-mail*</div>
              <input type="text" v-model="data.mail.value" class="form__input">
            </div>
          </div>
        </section>
        <div v-if="step !== 6" class="buttons">
          <div class="button__container">
            <button class="btn" v-on:click="previousStep" v-if="step > 1">Poprzedni</button>
          </div>
          <div class="button__container">
            <button class="btn" v-on:click="submitData"
                    v-if="step === 5">Złóż zamówienie</button>
            <button class="btn" v-on:click="nextStep" v-else>Następny</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Shop',
  data() {
    return {
      name: 'test',
      step: 1,
      loading: false,
      selected_shirt_side: 0,
      total_steps: 6,
      errors: [],
      data: {
        shirt_front: {
          form_name: 'shirt_front',
          value: false,
          price_modifier: 10,
          step: 1,
          confirmed: false,
        },
        shirt_rear: {
          form_name: 'shirt_rear',
          value: false,
          price_modifier: 10,
          step: 1,
          confirmed: false,
        },
        image: {
          form_name: 'image',
          value: null,
          seed: null,
          previous_seed: null,
          step: 2,
          confirmed: false,
        },
        grayscale: {
          form_name: 'grayscale',
          value: false,
          price_modifier: 2,
          step: 2,
          confirmed: false,
        },
        blur: {
          form_name: 'blur',
          value: 0,
          price_modifier: 3,
          max_value: 10,
          step: 2,
          confirmed: false,
        },
        name: {
          form_name: 'name',
          value: null,
          confirmed: false,
          step: 4,
          value_pattern: /[\S]+/,
          validation_message: 'Wprowadź poprawne imię',
        },
        surname: {
          form_name: 'surname',
          value: null,
          confirmed: false,
          step: 4,
          value_pattern: /[\S]+/,
          validation_message: 'Wprowadź poprawne nazwisko',
        },
        street: {
          form_name: 'street',
          value: null,
          confirmed: false,
          step: 4,
          value_pattern: /[\S]+/,
          validation_message: 'Wprowadź poprawną ulicę',
        },
        building: {
          form_name: 'building',
          value: null,
          confirmed: false,
          step: 4,
          value_pattern: /[\S]+/,
          validation_message: 'Wprowadź poprawny numer budynku',
        },
        flat: {
          form_name: 'flat',
          value: null,
          confirmed: false,
          step: 4,
        },
        mail: {
          form_name: 'mail',
          value: null,
          confirmed: false,
          step: 4,
          value_pattern: /^(([^<>()[\].,;:\s@"]+(\.[^<>()[\].,;:\s@"]+)*)|(".+"))@(([^<>()[\].,;:\s@"]+\.)+[^<>()[\].,;:\s@"]{2,})$/,
          validation_message: 'Wprowadź poprawny adres e-mail',
        },
        postal_code: {
          form_name: 'postal_code',
          value: null,
          confirmed: false,
          step: 4,
          value_pattern: /^[0-9]{2}-[0-9]{3}$/,
          validation_message: 'Wprowadź poprawny kod pocztowy',
        },
        city: {
          form_name: 'city',
          value: null,
          confirmed: false,
          step: 4,
          value_pattern: /[\S]+/,
          validation_message: 'Wprowadź poprawne miasto',
        },
        phone: {
          form_name: 'phone',
          value: null,
          confirmed: false,
          step: 4,
        },
      },
      steps: {
        1: {
          title: 'Wybierz gdzie ma zostać umieszczony nadruk',
        },
        2: {
          title: 'Skonfiguruj swoją grafikę',
        },
        3: {
          title: 'Potwierdź dane',
        },
        4: {
          title: 'Dane do rachunku',
        },
        5: {
          title: 'Potwierdź dane',
        },
        6: {
          title: 'Dziękujemy za zamówienie!',
        },
      },
    };
  },
  methods: {
    goToStep(object) {
      this.errors = [];
      this.step = object.step;
    },
    previousImage() {
      const actualSeed = this.data.image.seed;
      this.data.image.seed = this.data.image.previous_seed;
      this.data.image.previous_seed = actualSeed;
    },
    flipShirt() {
      if (this.selected_shirt_side === 0) {
        this.selected_shirt_side = 1;
      } else {
        this.selected_shirt_side = 0;
      }
    },
    decreaseBlur() {
      this.data.blur.value -= 1;
    },
    increaseBlur() {
      this.data.blur.value += 1;
    },
    validateActualStep() {
      const { step } = this;
      const errors = [];
      Object.keys(this.data).forEach((key) => {
        const object = this.data[key];
        if (object.step !== step || !object.value_pattern) return;
        const check = new RegExp(object.value_pattern);
        if (!check.test(object.value) || object.value === null) {
          errors.push(object.validation_message);
        }
      });
      if (!this.data.shirt_rear.value && !this.data.shirt_front.value) errors.push('Wybierz stronę nadruku');
      return errors;
    },
    nextStep() {
      this.errors = [];
      const validationErrors = this.validateActualStep();
      const confirmErrors = this.validateConfirms();
      this.errors = validationErrors.concat(confirmErrors);
      if (this.errors.length === 0) this.step += 1;
    },
    submitData() {
      this.errors = [];
      const errors = this.validateConfirms();
      this.errors = errors;
      const finalData = {};
      if (this.errors.length !== 0) return;
      Object.keys(this.data).forEach((key) => {
        finalData[this.data[key].form_name] = this.data[key].value;
      });
      finalData.total_price = this.totalPrice;
      this.step = 6;
      console.log(finalData);
    },
    validateConfirms() {
      let valid = true;
      let requiredKeys = [];

      if (this.step === 3) {
        requiredKeys = ['image', 'shirt_front', 'shirt_rear', 'grayscale', 'blur'];
      }

      if (this.step === 5) {
        requiredKeys = ['image', 'shirt_front', 'shirt_rear', 'grayscale', 'blur', 'building', 'city', 'flat', 'mail', 'phone', 'postal_code', 'street', 'surname', 'name'];
      }

      requiredKeys.forEach((key) => {
        if (!this.data[key].confirmed) {
          valid = false;
        }
      });
      if (!valid) {
        return ['Potwierdź wszystkie dane'];
      }
      return [];
    },
    confirmData(index) {
      this.data[index].confirmed = true;
      this.$forceUpdate();
    },
    confirmShirtSide() {
      this.data.shirt_rear.confirmed = true;
      this.data.shirt_front.confirmed = true;
      this.$forceUpdate();
    },
    previousStep() {
      this.errors = [];
      this.step -= 1;
    },
    randomizeImage() {
      this.data.image.previous_seed = this.data.image.seed;
      this.data.image.seed = Math.random().toString(36).substring(2, 15);
    },
    getFinalImage() {
      this.loading = true;
      let url = `https://picsum.photos/seed/${this.data.image.seed}/400?`;
      if (this.data.grayscale.value) {
        url += 'grayscale&';
      }
      if (this.data.blur.value) {
        url += `blur=${this.data.blur.value}`;
      }
      fetch(url)
        .then((response) => {
          this.loading = false;
          this.data.image.value = response.url;
        });
    },
  },
  computed: {
    totalPrice() {
      let totalPrice = 0;
      Object.keys(this.data).forEach((key) => {
        if (this.data[key].value && this.data[key].price_modifier) {
          totalPrice += this.data[key].price_modifier;
        }
      });
      return totalPrice;
    },
  },
  watch: {
    'data.grayscale.value': {
      handler() {
        this.data.grayscale.confirmed = false;
        this.getFinalImage();
      },
    },
    'data.image.seed': {
      handler() {
        this.data.image.confirmed = false;
        if (this.data.image.seed !== null) {
          this.getFinalImage();
        }
      },
    },
    'data.blur.value': {
      handler() {
        this.data.blur.confirmed = false;
        this.getFinalImage();
      },
    },
    'data.building.value': {
      handler() {
        this.data.building.confirmed = false;
      },
    },
    'data.city.value': {
      handler() {
        this.data.city.confirmed = false;
      },
    },
    'data.flat.value': {
      handler() {
        this.data.flat.confirmed = false;
      },
    },
    'data.mail.value': {
      handler() {
        this.data.mail.confirmed = false;
      },
    },
    'data.name.value': {
      handler() {
        this.data.name.confirmed = false;
      },
    },
    'data.phone.value': {
      handler() {
        this.data.phone.confirmed = false;
      },
    },
    'data.postal_code.value': {
      handler() {
        this.data.postal_code.confirmed = false;
      },
    },
    'data.shirt_front.value': {
      handler() {
        this.data.shirt_front.confirmed = false;
      },
    },
    'data.shirt_rear.value': {
      handler() {
        this.data.shirt_rear.confirmed = false;
      },
    },
    'data.street.value': {
      handler() {
        this.data.street.confirmed = false;
      },
    },
    'data.surname.value': {
      handler() {
        this.data.surname.confirmed = false;
      },
    },
  },
  created() {
    this.randomizeImage();
  },
};
</script>

<style scoped>

h1 {
  padding: 0;
}

.shop {
  padding-left: 20px;
  padding-right: 20px;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  column-gap: 20px;
  justify-content: center;
  flex-grow: 1;
}

.container--left {
  max-width:600px;
}

.container--right {
  width: 600px;
}

.whitebox {
  background-color:white;
  width: 100%;
  -webkit-box-sizing: border-box; /* Safari/Chrome, other WebKit */
  -moz-box-sizing: border-box;    /* Firefox, other Gecko */
  box-sizing: border-box;         /* Opera/IE 8+ */
  padding: 10px;
  margin-bottom: 20px;
  border-radius: 4px;
  box-shadow: 5px 5px 5px rgba(0,0,0,0.3);
  font-family: 'Manrope', sans-serif;
}

.priceBox {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  gap: 10px;
  justify-content: center;
}

.priceBox__value {
  background-image: linear-gradient(-180deg, #FF5E3A 0%, #FF9500 100%);
  font-size: 20px;
  border-radius: 5px;
  padding-left: 20px;
  padding-right: 20px;
  color: white !important;
}

.priceBox__text {
  font-size: 20px;
  font-weight: 700;
}

.shirt {
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  overflow: hidden;
}

.shirt__preview {
  position: relative;
}

.shirt__background {
  width: 100%;
}

.shirt__image {
  position: absolute;
  display:flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  top: 0;
  bottom: 0;
}

.confirm__header {
  text-align: center;
  padding-top: 10px;
  padding-bottom: 10px;
  margin-bottom: 10px;
  border: gray 2px solid;
  border-radius: 4px;
}

.shirt__image img {
  max-width: 200px;
}

.confirm {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  align-items: center;
  gap: 10px;
  margin-bottom: 10px;
}

.form__row {
  margin-bottom: 10px;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  width: 100%;
  justify-content: center;
  align-items: center;
}

.form__title {
  width: 70%;
  text-align: left;
}

.form__row input {
  width: 70%;
  background-color: #FFF;
  color: #222;
  border-width: 1px;
  border-style: solid;
  border-color: #AAA;
  border-radius: 4px;
  box-shadow: 0 0 8px -3px #888;
  padding: 7px;
  font-size: 15px;
}

.form__row input:focus {
  outline-color: orange;
  outline-width: 2px;
  outline-style: solid;
}

.confirm__text {
  width: 50%;
  text-align: right;
}

.confirm__data {
  font-weight: 600;
}

.btn {
  background-image: linear-gradient(180deg, #C445FC 0%, #6155D9 100%);
  border-radius: 4px;
  box-sizing: border-box;
  color: #FFFFFF;
  display: flex;
  justify-content: center;
  padding: 0.2rem 1rem;
  text-decoration: none;
  border: 0;
  cursor: pointer;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  font-family: 'Manrope', sans-serif;
  font-size: 13px;
}

.btn:hover {
  background-image: none;
  background-color: #8C30F5;
}

.btn--rotate {
  background-color: orange!important;
  background-image: none;
  font-size: 20px;
  margin-bottom: 20px;
}

.btn--rotate:hover {
  background-color: darkorange!important;
  background-image: none;
}

.btn__edit {
  background-image: none;
  color: black;
}

.btn__edit:hover {
  background-image: none;
  background-color: #a094c4;
}

.btn__confirm {
  background-image: none;
  outline: #a094c4 solid 2px;
  color: black;
}

.btn__confirm:hover {
  background-image: none;
  background-color: #a094c4;
}

.form {
  margin-bottom: 30px;
}

.mainimg {
  display: flex;
  justify-content: center;
  margin-top: 10px;
  height: 400px;
}

.mainimg__preview {
  outline-color: gray;
  outline-style: solid;
}

.errors {
  padding: 0;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 0;
  margin-bottom: 20px;
}

.errors__row {
  outline-style: solid;
  outline-width: 2px;
  padding: 5px;
  outline-color: rgba(255,0,0,0.5);
  background-color: rgba(255,0,0,0.5);
  border-radius: 4px;
  margin: 0;
}

.confirm__checkmark {
  max-height: 20px;
}

.image-editor {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
}

.imageTools__row {
  display: flex;
  justify-content: center;
  margin-top: 10px;
  gap: 10px;
}

.step__title {
  padding: 0;
  text-align: center;
  font-size: 20px;
}

.buttons {
  margin-top: 40px;
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: space-between;
}

.thankYou {
  display: flex;
  height: 70vh;
  justify-content: center;
  align-content: center;
  align-items: center;
  font-family: 'Manrope', sans-serif;
  color: white;
  font-size: 60px;
  font-weight: 700;
}

.animated__shake {
  animation: shake 1s;
}

.animated__fadein {
  animation: fadein 1s;
}

.loader {
  width: 400px;
  height: 400px;
  display: flex;
  align-items: center;
  align-content: center;
  justify-content: center;
}

.loader__icon {
  width: 30px;
  height: 30px;
  -webkit-animation:spin 1s linear infinite;
  -moz-animation:spin 1s linear infinite;
  animation:spin 1s linear infinite;
}

@keyframes shake {
  10%, 90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%, 80% {
    transform: translate3d(2px, 0, 0);
  }

  30%, 50%, 70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%, 60% {
    transform: translate3d(4px, 0, 0);
  }
}

@keyframes fadein {
  from { opacity: 0; }
  to   { opacity: 1; }
}

/* Firefox < 16 */
@-moz-keyframes fadein {
  from { opacity: 0; }
  to   { opacity: 1; }
}

/* Safari, Chrome and Opera > 12.1 */
@-webkit-keyframes fadein {
  from { opacity: 0; }
  to   { opacity: 1; }
}

/* Internet Explorer */
@-ms-keyframes fadein {
  from { opacity: 0; }
  to   { opacity: 1; }
}

/* Opera < 12.1 */
@-o-keyframes fadein {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@-moz-keyframes spin {
  100% { -moz-transform: rotate(360deg); }
}
@-webkit-keyframes spin {
  100% { -webkit-transform: rotate(360deg); }
}
@keyframes spin {
  100% {
    -webkit-transform: rotate(360deg);
    transform:rotate(360deg);
  }
}

@media screen and (max-width: 600px) {
  .shirt__image img {
    max-width: 30vw;
  }
  .container--right {
    width: 100%;
  }
  .shop {
    flex-direction: column;
  }
  .mainimg__preview {
    width:100%;
  }
}

</style>
