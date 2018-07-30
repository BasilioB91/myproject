<template>
    <div id="get-currencies">
        <h2>All Currencies</h2>
        <div class="columns medium-2" v-for="(x, index) in results.Rates.Rate" v-bind:key=index>
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

export default {
    name: 'Currencies',
    data(){
        return {
            results: '',
            xml: ''
        }
    },
     mounted() {
        const proxyurl = "https://cors-anywhere.herokuapp.com/";
        const url = "http://rates.fxcm.com/RatesXML";
        var parseString = require('xml2js').parseString;
        var self = this;
        axios.get(proxyurl + url)
        .then(response => {console.log(response.data), this.xml = response.data,
        parseString(response.data, function (err, result) {
            self.results=result
        });
        });
    }
}
</script>

<style>

</style>
