<template>
    <div id="get-currencies">
        
        <!-- Adding in the fields to select Currency and rate limit -->
        <!-- start -->
        <div class="row">
    <div class="large-4 columns">
      <label>Select Currency
        <select name="" v-model="selectedCurrency">
                <option v-for="(x, index) in results.Rates.Rate" :value="x.$.Symbol" v-bind:key="index">{{x.$.Symbol}}</option>
            </select>
      </label>
    </div>
    <div class="large-3 columns">
      <label>Min Limit
        <input type="number" v-model="minRate" min="0" placeholder="Enter your min cutoff rate" />
      </label>
    </div>
    <div class="large-3 columns">
        <label>Max Limit</label>
        <input type="number" v-model="maxRate" min="0" placeholder="Enter your max cutoff rate" />
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
                    <p>{{x.$.Symbol}}</p>
                </div>
                <div class="card-divider">
                    <p>{{x.Ask[0]}}</p>
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
           // minRate:0,
            //maxRate:1,
            //selectedCurrency:"EURUSD",
            interval:null
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
            clearInterval(this.interval)
            var self=this;
            this.interval=setInterval(function(){
                self.getCurrencies().then(response => {
                    
                    parseString(response.data, function (err, result) {
                        //adding page refresher
                        self.results=result
                        let tempArr=result.Rates.Rate.filter((item,index)=>{
                            if(item.$.Symbol==self.selectedCurrency){
                                return true;
                            }
                            return false;
                        })
                        if(tempArr[0].Bid[0]>self.maxRate){
                            alert("Upper Limit Reached")
                        }
                        if(tempArr[0].Bid[0]<self.minRate){
                            alert("lower Limit Reached")
                        }
                        //Remove if not required
                        //else{
                            //alert("Rate between Min and Max")
                        //}
                        console.log(tempArr)
                    });
                });;
            },15000)
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
