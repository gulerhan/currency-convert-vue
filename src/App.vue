<template>
  <v-app style="background-color: #f6f6f6">
    <div class="pt-20">
      <p
        class="flex justify-center text-2xl font-bold font-mono"
        style="color: #1f2261"
      >
        Currency Converter
      </p>
      <div class="pt-8">
        <v-card
          class="p-12 ml-auto mr-auto rounded-lg shadow-lg w-1/4"
          id="mediaQuery"
        >
          <div class="flex flex-row align-center justify-between pb-10">
            <div class="p-2">
              <p class="text-xs text-slate-500 font-mono pb-1">Amount</p>
              <div class="dropdown inline-block relative">
                <button class="" style="width: 150px" v-if="toCurrency">
                  <v-select
                    v-model="fromCurrency"
                    density="comfortable"
                    label=""
                    :items="currencies"
                    item-title="name"
                    item-value="name"
                    :hint="`${fromCurrency.value}`"
                    return-object
                    persistent-hint
                    single-line
                  ></v-select>
                </button>
              </div>
            </div>

            <div class="flex flex-row justify-between align-center">
              <v-text-field
                label="Type here.."
                maxlength="10"
                hide-details="auto"
                class=""
                style="width: 150px"
                v-model="fromAmount"
                v-on:keyup="convert()"
              ></v-text-field>
            </div>
          </div>
          <!----------------------->
          <div class="relative flex justify-center">
            <hr class="w-100" />
            <v-btn
              class="rounded-full absolute top-[-25px] min-w-0 text-white"
              fab
              dark
              small
              style="
                height: 55px;
                box-shadow: none;
                transition-duration: 1s;
                background-color: #26278d;
              "
              @click="btnSwap()"
              :style="{
                transform: active ? 'rotate(360deg)' : 'rotate(0deg)',
              }"
            >
              <v-icon dark class="text-2xl"> mdi-autorenew </v-icon>
            </v-btn>
          </div>
          <div class="flex flex-row align-center justify-between pt-10">
            <div class="p-2">
              <p class="text-xs text-slate-500 font-mono pb-1">
                Currency Amount
              </p>

              <div class="dropdown inline-block relative">
                <button class="" style="width: 150px" v-if="toCurrency">
                  <v-select
                    v-model="toCurrency"
                    density="comfortable"
                    label=""
                    :items="currencies"
                    item-title="name"
                    item-value="name"
                    :hint="`${toCurrency.value}`"
                    return-object
                    persistent-hint
                    single-line
                  ></v-select>
                </button>
                <!-- <ul
                  class="dropdown-menu absolute hidden text-gray-00 pt-1 z-10"
                >
                  <li
                    class="rounded-t bg-gray-100 hover:bg-gray-400 py-2 px-4 block whitespace-no-wrap"
                    v-for="(value, key) in currencies"
                    :key="key"
                  >
                    {{ key }}
                  </li>
                   <li class="">
                    <a
                      class="rounded-t bg-gray-100 hover:bg-gray-400 py-2 px-4 block whitespace-no-wrap"
                      href="#"
                      >One</a
                    >
                  </li> -->
                <!-- </ul>  -->
              </div>
            </div>
            <div class="flex flex-row justify-end align-center">
              <v-text-field
                disabled
                hide-details="auto"
                class=""
                style="width: 150px"
                v-model="toAmount"
              ></v-text-field>
            </div>
          </div>
        </v-card>
        <div class="pt-6">
          <p class="text-xl flex justify-center pt-4" style="color: #a1a1a1">
            Indicative Exchange Rate
          </p>
          <p class="flex text-xl justify-center pt-4 font-mono font-bold">
            {{ fromAmount }} {{ fromCurrency.name }} = {{ toAmount }}
            {{ toCurrency.name }}
          </p>
        </div>
      </div>
    </div>
  </v-app>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data: () => ({
    active: false,
    units: null,
    currencies: [],
    fromCurrency: null,
    toCurrency: null,
    fromAmount: "",
    toAmount: "",
  }),
  methods: {
    btnSwap() {
      this.active = !this.active;
      const tmpFrom = this.fromCurrency;
      const tmpTo = this.toCurrency;
      const tmpFromAmount = this.fromAmount;
      const tmpToAmount = this.toAmount;
      this.fromAmount = tmpToAmount;
      this.toAmount = tmpFromAmount;
      this.fromCurrency = tmpTo;
      this.toCurrency = tmpFrom;
    },
    convert() {
      axios
        .get(
          `https://v6.exchangerate-api.com/v6/7364bf845061862503da3581/pair/${this.fromCurrency.name}/${this.toCurrency.name}`
        )
        .then((res) => {
          this.toAmount = (this.fromAmount * res.data.conversion_rate).toFixed(
            1
          );
        });
    },
  },
  created: function () {
    axios
      .get("https://v6.exchangerate-api.com/v6/7364bf845061862503da3581/codes")
      .then((res) => {
        for (const code of res.data.supported_codes) {
          this.currencies.push({
            name: code[0],
            value: code[1],
          });
        }
        this.fromCurrency = this.currencies.find((curr) => curr.name == "TRY");
        this.toCurrency = this.currencies.find((curr) => curr.name == "USD");
      });
  },
};
</script>
