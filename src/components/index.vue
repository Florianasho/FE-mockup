<template>
  <div class="container">
    <div class="row">
      <div class="col-12 col-md-4 mx-auto">
        <div class="card">
          <div class="card-header">
            <div>USD - United States Dollars</div>
            <div>
              <span>USD</span>
              <input type="number" v-model="number_input" min=10 class="form-control">
            </div>
          </div>
          <div class="card-body content-body">
            <div class="content row" v-for="(display_rates_row, index) in display_rates" :key="index">
              <div class="col-10 box">
                <div class="row">
                  <div class="col-4">{{display_rates_row.name}}</div>
                  <div class="exchange col-8">{{ (display_rates_row.rates.toFixed(2) * number_input).toFixed(2)}}</div>
                </div>
                <p class="desc">{{display_rates_row.name +' - ' + txt[display_rates_row.name] }}</p>
                <p>{{ '1 USD = ' + display_rates_row.name + ' ' + display_rates_row.rates.toFixed(2) }}</p>
              </div>
              <div class="col-2 border-left" @click="deleteClass(index)">
                <div class="delete"><i class="fa fa-trash"></i></div>
              </div>
            </div>
            <button type="button" class="btn btn-dark btn-add btn-block" @click="input_currency = true" v-if="input_currency === false">Add More Currencies</button>
            <div class="new_currency" v-else>
              <div class="input-group mt-4">
                <select class="custom-select" id="currency" v-model="selected_currency">
                  <option value= '' hidden>Choose...</option>
                  <option :value="rates_row" v-for="(rates_row, index_rates) in rates_list" :key="index_rates">{{ rates_row }}</option>
                </select>
                <div class="input-group-append">
                  <button class="btn btn-outline-secondary" type="submit" @click="addCurrency()">Submit</button>
                </div>
              </div>
              <span @click="input_currency = false">Cancel</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      input_currency: false,
      display_rates: [],
      rates: {},
      txt: {
        'IDR': 'Indonesian Rupiah',
        'CHF': 'Swiss Franc',
        'SGD': 'Singapore Dollar',
        'INR': 'Indian Rupee',
        'MYR': 'Malaysian Ringgit',
        'JPY': 'Japanese Yen',
        'KRW': 'Korean Won',
        'GBP': 'British Pound',
        'CAD': 'Canadian Dollar',
        'USD': 'United States Dollar'
      },
      selected_currency: '',
      number_input: 10,
      rates_list: [ 'IDR', 'CHF', 'SGD', 'INR', 'MYR', 'JPY', 'KRW', 'GBP', 'CAD', 'USD' ]
    }
  },
  created () {
    this.getCurrency()
  },
  methods: {
    getCurrency () {
      var self = this
      self.$axios
        .get('https://api.exchangeratesapi.io/latest?base=USD')
        .then(response => (
          self.rates = response.data.rates
        ))
        .catch(error => console.log(error))
    },
    addCurrency () {
      if (this.selected_currency !== '') {
        this.display_rates.push({name: this.selected_currency, rates: this.rates[this.selected_currency]})
        this.input_currency = false
      } else {
        alert('Pilih currency terlebih dahulu')
      }
    },
    deleteClass (index) {
      if (confirm('Apakah Anda Yakin Untuk Menghapus Currency?')) {
        this.display_rates.splice(index, 1)
        alert('Data berhasil dihapus')
      }
    }
  }
}
</script>
<style lang="stylus" scoped>
.card-header
  padding 15px 20px
  text-align left
  &>div:nth-child(2)
    display block
  span
    font-size 22px
    font-weight bold
  input
    width 100px
    float right
.content-body
  .content
    border 1px solid #b3b3b3
    margin 10px 0
    .box
      text-align left
      padding 10px
      p
        margin-bottom 0
        &.desc
          font-size 14px
          font-weight bold
          font-style italic
      div.row
        font-size 20px
        .exchange
          text-align right
          width 100%
    .delete
      margin auto
      display inline-flex
      height 100%
      i
        font-size 18px
        margin auto
  .btn-add
    margin-top 20px
    cursor pointer
    &:hover
      background-color lighten(#343a40, 10%)
  .new_currency
    text-align right
    span
      margin-top 10px
      font-size 12px
</style>
