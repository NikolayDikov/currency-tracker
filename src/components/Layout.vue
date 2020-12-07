<template>
  <div class="wrapper">
    <div class="currencyCon">
      <div class="header">
          <h1>{{ msg }}</h1>
      </div>
      <div class="convertCon">
        <div class="imputField">
          <div class="inputAmount"><label for="amount" class="Label">Amount</label><input class="inputField" id="amount" aria-label="Amount" name="Amount" type="text" placeholder="amount"></div>
          <div class="inputAmount"><label for="amount" class="Label">From</label><input class="inputField currencyAuto" id="from" aria-label="Amount" name="Amount" type="text" placeholder="currency"></div>
          <button class="inverseButton" type="button" v-on:click="inverse"><svg aria-hidden="true" data-id="icon-exchange" viewBox="0 0 50 47" height="32px" width="30px"><path fill="currentColor" fill-rule="evenodd" d="M49.897 35.977L26.597 25v7.874H7.144v6.207h19.455v7.874zM.103 11.642l23.3 10.977v-7.874h19.454V8.538H23.402V.664z"></path></svg></button>
          <div class="inputAmount"><label for="amount" class="Label">To</label><input class="inputField currencyAuto" id="to" aria-label="Amount" name="Amount" type="text" placeholder="currency"></div>
          <button class="submitEx" id="submitEx" type="submit" v-on:click="convert"><span class="glyphicon glyphicon-chevron-right"></span></button>
        </div>
        <div class="todayEx">
          <div class="loaderCon" v-if="loading">
            <div class="loader"></div>
          </div>
          <div class="currentRate" v-if="todayExchange!=null && periodResults.length > 29">
            <div id="info1">{{ amountConverted }} {{ cFrom }} =</div>
            <div id="info2"><span id="info21">{{ todayExchange }}</span><span id="info22">{{ cTo }}</span></div>
          </div>
          <div class="currentRate" v-if="errorMsg!=null">
            <div id="info1">{{ errorMsg }}</div>
          </div>
        </div>
      </div>
    </div>
    <div class="resultContainer">
      <div class="chartContainer">
        <div class="chart">
          <h2 class="chartHead"  v-if="periodResults.length > 29">{{ cFrom }} to {{ cTo }} Chart <span class="addInfo">The green tick on the chart represents which was the best day for exchange in the past 30 days!</span></h2>
          <h2 class="chartHead"  v-if="periodResults.length <= 29">You will see a chart with the past exchange rates for the perod of 30 days, after you make the exchange.</h2>
          <LineChart
            :chartData="periodResults"
            :pointLabel="currencyChartLabel"
            :today="todayExchange"
            :lineChartColors="chartColors"
            :pointColors="pointColorsArr"
            v-if="periodResults.length > 29"
          />
        </div>
      </div>
    </div>
    
  </div>
</template>

<script>
import axios from "axios";
import Chart from 'chart.js';
import LineChart from "./LineChart";
const AccessKey = "932a604ee898ad7f7496f30243ef0b56";

export default {
  name: 'Layout',
  props: {
    msg: String
  },
  components: {
    LineChart
  },
  data () {
    return {
      chartColors: {blue: 'rgba(0, 34, 114, 0.900)',yellow: 'rgb(252, 184, 19)',green: '#32cd32'},
      pointColorsArr: [],
      info: null,
      currencies: null,
      periodResults:[],
      currencyChartLabel: null,
      todayExchange: null,
      cTo: null,
      cFrom: null,
      amountConverted: null,
      loading: false,
      errorMsg: null
    }
  },
  mounted () {
      
      axios
        .get('https://api.currencylayer.com/list?access_key='+AccessKey)
        .then(response => (this.currencies = response.data.currencies))
      setTimeout(() => {
          const CurrencyArray = Object.keys(this.currencies).map((key) => String(key));
          $(".currencyAuto").autocomplete({
            source: CurrencyArray
          });
      }, 1000);
      
    
  },
  methods: {
    inverse(){
      let holdMe = $('#from').val();
      $('#from').val($('#to').val());
      $('#to').val(holdMe);
    },
    convert(){
      this.periodResults = [];
      this.loading = true;
      this.errorMsg = null;
      let amout = $('#amount').val();
      let from = $('#from').val();
      let to = $('#to').val();
    	axios
        .get('https://api.currencylayer.com/convert?from='+from+'&to='+to+'&amount='+amout+'&access_key='+AccessKey)
        .then(response => { 
          let respHolder = response.data;
          if (response.data.success) {
            this.todayExchange = response.data.result;
            this.periodData(amout,from,to);
            this.currencyChartLabel = to;
            this.cTo = to;
            this.cFrom = from;
            this.amountConverted = amout;
          }else{
            this.loading = false;
            this.errorMsg = response.data.error.info;
          }
        });
        // response.data.error.info
    },
    async periodData(amoutParm,fromParm,toParm){
      let oneSunnyDayInThePast,oneSunnyDayInThePastFormated,tempDate,chartDate;
      let j=0;
      let highPointIndex = 0;
      let highPoint = 0;
      const pointColors = [];
      for (let i = 30; i > 0; i--) {
        oneSunnyDayInThePast = new Date(new Date().setDate(new Date().getDate() - i));
        if (oneSunnyDayInThePast.getDate().toString().length==1) {
          tempDate="0";
        }else{
          tempDate="";
        }
        oneSunnyDayInThePastFormated = oneSunnyDayInThePast.getFullYear().toString()+"-"+(oneSunnyDayInThePast.getMonth()+1).toString()+"-"+tempDate+oneSunnyDayInThePast.getDate().toString();
        chartDate = tempDate+oneSunnyDayInThePast.getDate().toString()+"/"+(oneSunnyDayInThePast.getMonth()+1).toString();
        
        try {
          let response = await axios.get('https://api.currencylayer.com/convert?from='+fromParm+'&to='+toParm+'&amount='+amoutParm+'&date='+oneSunnyDayInThePastFormated+'&access_key='+AccessKey);
          this.periodResults.push({chartDate , res: response.data.result});
          if (response.data.result > highPoint) {
            highPoint = response.data.result;
            highPointIndex = j;
          }
          pointColors.push(this.chartColors.blue);
        } catch (error) {
          console.error(error);
        }
        j++;
      }
      pointColors[highPointIndex] = this.chartColors.green;
      this.pointColorsArr = pointColors;
      this.loading = false;
    }
}
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.todayEx {
  display: flex;
  justify-content: center;
  height: 100px;
}
.inverseButton {
  font-size: inherit;
  text-align: center;
  transition: background 0.25s ease 0s;
  line-height: 1;
  letter-spacing: 0.03em;
  padding: 14px 10px;
  border-radius: 5px;
  background: transparent;
  border: 0px;
  color: white;
  height: 60px;
  margin: 0px 5px;
  width: 60px;
  appearance: button;
  cursor: pointer;
    display: flex;
    align-self: flex-end;
}
.addInfo {
  display: block;
  font-weight: 600;
  font-size: 1.4rem;
}
.chartHead {
  font-size: 2.4rem;
  color: rgb(0, 34, 114);
  margin: 0px 0px 30px;
  font-weight: 700;
  line-height: 1.3;
}
.chart {
  width: 100%;
}
.resultContainer {
  background-color: rgb(181, 215, 253);
  color: black;
}
.chartContainer {
  border-top: 5px solid rgb(252, 184, 19);
  border-bottom: 5px solid rgb(252, 184, 19);
  width: 80%;
  display: flex;
  align-self: center;
  justify-content: center;
  margin: auto;
  padding-top: 2em;
  background-color: white;
}
#info1 {
  font-size: 2.4rem;
}
#info2 {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
#info21 {
  font-size: calc(60.25px);
  line-height: calc(60.25px);
}
#info22 {
  font-size: calc(27.0833px);
  line-height: normal;
  padding-bottom: 4px;
  margin-top: 4px;
  padding-left: 10px;
  display: flex;
  justify-content: center;
  align-items: flex-end;
}
.loaderCon {
  width: 60px;
  height: 60px;
  align-self: center;
}
.loader {
  border: 16px solid #f3f3f3;
  border-radius: 50%;
  border-top: 16px solid #3498db;
  width: 60px;
  height: 60px;
  -webkit-animation: spin 2s linear infinite; /* Safari */
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
.submitEx{
    height: 60px;
    margin: 0px;
    max-width: 60px;
    min-width: 0px;
    font-size: inherit;
    text-align: center;
    transition: background 0.25s ease 0s;
    padding: 14px 10px;
    color: rgb(0, 34, 114);
    background: rgb(252, 184, 19);
    border-radius: 10px;
    align-items: center;
    border: 0px;
    display: flex;
    align-self: flex-end;
    justify-content: center;
    width: 100%;
}
.inputField{
    background: rgb(255, 255, 255);
    box-sizing: border-box;
    border: 0px;
    border-radius: 10px;
    color: rgb(0, 34, 114);
    font-size: 3rem;
    height: 60px;
    padding-left: 20px;
    width: 90%;
}
.Label{
    color: rgb(255, 255, 255);
    display: block;
    margin: 0px 15px 5px;
    text-align: left;
}
.imputField{
  width: 80%;
  display: flex;
  justify-content: space-between;
  margin: auto;
  padding: 20px 1px 10px;
}
.convertCon{
  background-color: rgba(0, 34, 114, 0.900);
  width: 80%;
  border-radius: 10px 10px 0px 0px;
  display: flex;
  flex-direction: column;
  margin: auto;
}
.currencyCon{
  background-image: url("../assets/layoutback.jpg");
  background-repeat: no-repeat;
  border-bottom: 5px solid  rgba(0, 34, 114, 1);
  background-position: center;
  padding-top: 2%;
  display: flex;
  justify-content: center;
  flex-direction: column;
}

/* Mobile changes */
@media screen and (max-width: 900px) {
    .imputField {
        width: 100%;
        flex-direction: column;
        justify-content: center;
    }
    .inverseButton {
      display: none;
    }
    .submitEx {
      width: 90%;
      height: 50px;
      max-width: 100%;
      align-self: center;
      margin-top: 20px;
    }
    .Label {
      margin: auto;
      width: 90%;
    }
    .inputField {
      height: 50px;
    }
    #info21 {
      font-size: calc(40.25px);
      line-height: calc(40.25px);
    }
    #info22 {
      font-size: calc(20.0833px);
      padding-bottom: 1px;
    }
    .todayEx {
      height: 80px;
    }
}
</style>
