<template>
    <div id="get-currencies">
        <!-- Adding in the fields to select Currency and rate limit -->
        <!-- start -->
        <div class="row">
    <div class="large-2 columns">
      <label>Select Currency
        <select name="" v-model="selectedCurrency">
                <option v-for="(x, index) in results.Rates.Rate" :value="x.$.Symbol" v-bind:key="index">{{x.$.Symbol}}</option>
            </select>
      </label>
    </div>
    <div class="large-2 columns">
      <label>Select Interval
        <select name="" v-model="interval">
                <option v-for="(thisvalue,index) in intervalValues" v-bind:value="thisvalue.value" v-bind:key="index">
                    {{thisvalue.text}}
                </option>
        </select>
      </label>
    </div>
    <div class="large-3 columns">
      <label>Min Limit (price you want it to drop to)
        <input type="number" v-model="minRate" min="0" step="any" placeholder="Enter your min cutoff rate" />
      </label>
    </div>
    <div class="large-3 columns">
        <label>Max Limit (price you want it to reach)</label>
        <input type="number" v-model="maxRate" min="0" step="any" placeholder="Enter your max cutoff rate" />
    </div>
     <div class="large-2 columns">
      <label>&nbsp;</label>
        <a href="#" @click.prevent="watchCurrency" class="button">Watch</a>
    </div>
  </div>
        <hr>
        <h2>All Currencies</h2>
        <!-- End -->
        <div class="columns medium-2" v-for="(x, index) in results.Rates.Rate" v-bind:key="index">
            <div class="card">
                <div class="card-section">
                    <p align="center">{{x.$.Symbol}}</p>
                </div>
                <div class="card-divider">
                    <p>Bid - {{x.Bid[0]}}</p>
                </div>
                <div class="card-section">
                    <p>Ask - {{x.Ask[0]}}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
const proxyurl = "https://cors-anywhere.herokuapp.com/";
const url = "http://rates.fxcm.com/RatesXML";
var parseString = require('xml2js').parseString;
export default {
    
    name: 'Currencies',
    data(){
        return {
            results: '',
            xml: '',
            minRate: undefined,
            maxRate: undefined,
            selectedCurrency:'',
            interval: undefined,
            intervalValues: [
                {text: '1 Second', value: '1000'},
                {text: '5 Seconds', value: '5000'},
                {text: '10 Seconds', value: '10000'},
                {text: '15 Seconds', value: '15000'},
                {text: '30 Seconds', value: '30000'},
                {text: '1 Minute', value: '60000'}
            ]
        }
    },
    //Watch property to watch the minrate and max rate change
    watch:{
        minRate:function(newVal,oldVal){
            console.log(newVal)
            if(newVal>this.maxRate){
                this.minRate=oldVal
            }
        },
         maxRate:function(newVal,oldVal){
            if(newVal<this.minRate){
                this.maxRate=oldVal
            }
        }
    },
    //Methods section to handle user input
    methods:{
        watchCurrency(){
            //clearInterval(this.interval)
            var self=this;
            var thisMinRate = this.minRate;
            var thisMaxRate = this.maxRate;
            var thisCurrency = this.selectedCurrency;
            var myInterval = this.interval;
            this.minRate = undefined;
            this.maxRate = undefined;
            this.interval = undefined;
            this.selectedCurrency='';
            var thisTimer=setInterval(function(){
                self.getCurrencies().then(response => {
                    parseString(response.data, function (err, result) {
                        //adding page refresher
                        self.results=result
                        let tempArr=result.Rates.Rate.filter((item,index)=>{
                            if(item.$.Symbol==thisCurrency){
                                return true;
                            }
                            return false;
                        })
                        if(tempArr[0].Bid[0]>=thisMaxRate){
                            clearInterval(thisTimer);
                            alert("The asking bid for "+ thisCurrency +" has reached and/or crossed above "+thisMaxRate);
                        }
                        if(tempArr[0].Bid[0]<=thisMinRate){
                            clearInterval(thisTimer);
                            alert("The asking bid for "+ thisCurrency +" has dropped to and/or crossed below "+thisMinRate);
                        }
                    
                        console.log(tempArr)
                    });
                });;
            },myInterval)
        },
        getCurrencies(){
            return axios.get(proxyurl + url)
        }
    },
     mounted() {
        var self=this;
        this.getCurrencies().then(response => {
            console.log(response.data), this.xml = response.data,
            parseString(response.data, function (err, result) {
                self.results=result
            });
        });;
    }
}
</script>

<style scoped>

</style>
