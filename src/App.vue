<template>
    <div id="app">
        <h1 id="header"><span style="color: #235F9C">The Chronicle</span> Pseudo-Blossom</h1>
        <h2>PJMS 361S Philip Napoli // Robbie Ha & Elizabeth Chiu</h2>
        
        <textarea name="message" rows="2" cols="40" placeholder="What is your potential headline?" v-model="newHeadline">
        </textarea>
        <button class="btn" @click="addHeadline">Add Headline</button>
        <button class="btn" @click="sortByPredictability">Order By Predictability Score</button>
        <hr>
        <div id="layouter">
            <ul style="list-style-type:none">
                <li class="entries" v-for="headline of headlines">
                    <p class="biggerBold">POST MESSAGE: {{ headline.title }}</p>
                    <p class="smaller">COMPOUND VALENCE SCORE: {{ headline.sentiment.compound }} & NEG: {{ headline.sentiment.neg }} NEUTRAL: {{ headline.sentiment.neu }} POS: {{ headline.sentiment.pos }}</p>
                    <p class="biggerBold">HA-CHIU PREDICTABILITY SCORE: {{ headline.PredictabilityScore }}</p>
                    <p>Matches w/ words btw. 9th & 10th Decile*: {{ headline.firstMatch }}</p>
                    <p class="verySmall">*Of articles ranked by the number of unique people who engaged in certain ways with the Page post, for example by commenting on, liking, sharing, or clicking upon particular elements of the post (Unique Users) - Gathered by Facebook Analytics, Courtesy of The Chronicle</p>
                    <button class="btn" @click="removeHeadline(headline)">Delete</button>
                </li>
            </ul>
        </div>
  </div>
</template>

<script>
import first from './assets/0_10.json'
import second from './assets/10_20.json'
import third from './assets/20_30.json'
import fourth from './assets/30_40.json'
import fifth from './assets/40_50.json'
import sixth from './assets/50_60.json'
import seventh from './assets/60_70.json'
import eighth from './assets/70_80.json'
import ninth from './assets/80_90.json'
import tenth from './assets/90_100.json'

export default {
  name: 'app',
  data () {
    return {
        msg: 'Welcome to Your Vue.js App',
        newHeadline:'',
        rankGenerate: false,
        headlines: [],
        localFirst: first,
        localSecond: second,
        localThird: third,
        localFourth: fourth,
        localFifth: fifth,
        localSixth: sixth,
        localSeventh: seventh,
        localEighth: eighth,
        localNinth: ninth,
        localTenth: tenth
        }
    },
    components: {
    },
    methods: {
        sentimentScore(input) {
            const vader = require('vader-sentiment');
            const intensity = vader.SentimentIntensityAnalyzer.polarity_scores(input);
            return intensity;
        },
        addHeadline () {
            var sentimentScore = this.sentimentScore(this.newHeadline);
            var mPredictabilityScore = this.predictabilityAlgorithm(this.newHeadline);
            if (this.newHeadline) {
                this.headlines.push({
                    title: this.newHeadline,
                    sentiment: sentimentScore,
                    PredictabilityScore: mPredictabilityScore[0],
                    firstMatch: mPredictabilityScore[1],
                    secondMatch: mPredictabilityScore[2],
                    thirdMatch: mPredictabilityScore[3],
                    fourthMatch: mPredictabilityScore[4],
                    fifthMatch: mPredictabilityScore[5],
                    sixthMatch: mPredictabilityScore[6],
                    seventhMatch: mPredictabilityScore[7],
                    eighthMatch: mPredictabilityScore[8],
                    ninthMatch: mPredictabilityScore[9],
                    tenthMatch: mPredictabilityScore[10]
                    
                    
                })
                // text input displays this value, so clear it to indicate ready to type a new one
                
            }
            this.newHeadline = '';
        },
        
        checkMatches (headline,chosenArray) {
            var headlineArray = headline.split(" ");
            var headlineArrayLower = headlineArray.join('|').toLowerCase().split('|');
            var intersectionArray = chosenArray.filter(function(n) {
                return headlineArrayLower.indexOf(n) !== -1;
            });
            return intersectionArray.length;
        },
        
        predictabilityAlgorithm (headline) {
            var firstMatches = this.checkMatches(headline,this.localFirst);
            var secondMatches = this.checkMatches(headline,this.localSecond);
            var thirdMatches = this.checkMatches(headline,this.localThird);
            var fourthMatches = this.checkMatches(headline,this.localFourth);
            var fifthMatches = this.checkMatches(headline,this.localFifth);
            var sixthMatches = this.checkMatches(headline,this.localSixth);
            var seventhMatches = this.checkMatches(headline,this.localSeventh);
            var eighthMatches = this.checkMatches(headline,this.localEighth);
            var ninthMatches = this.checkMatches(headline,this.localNinth);
            var tenthMatches = this.checkMatches(headline,this.localTenth);
            var sentimentScore = this.sentimentScore(headline);
            var PredictabilityScore = 9*(firstMatches)+8*(secondMatches)+7*(thirdMatches)+6*(fourthMatches)+5*(fifthMatches)+4*(sixthMatches)+3*(seventhMatches)+2*(eighthMatches)+1*(ninthMatches);
            if(Math.abs(sentimentScore) <= 0.5) {
                PredictabilityScore = PredictabilityScore*(1.10);
            }
            return [PredictabilityScore,firstMatches,secondMatches,thirdMatches,fourthMatches,fifthMatches,sixthMatches,seventhMatches,eighthMatches,ninthMatches,tenthMatches];
            
        },
        

        // remove given todo from the list
        removeHeadline (headline) {
            this.headlines.splice(this.headlines.indexOf(headline), 1)
        },
        
        compareByPredictability(a,b) {
            if (a.PredictabilityScore < b.PredictabilityScore){
                return 1;
            }
                
            if (a.PredictabilityScore > b.PredictabilityScore){
                return -1;
            }
            return 0;
        },
        
        sortByPredictability() {
            this.headlines = this.headlines.sort(this.compareByPredictability);
        }
        
        
    }
}

</script>

<style lang="scss">
#app {
    font-family: 'Rajdhani', sans-serif;
    margin: 25px 50px 25px 50px;
    

}
#header {
    font-size:60px;
}
#theBest {
    color: "#001A57";
}
.entries {
    
    font-size: 15px;
}

#layouter {
    margin: 0px 50px 0px 20px;
}

.biggerBold {
    font-size:25px;
}
.verySmall {
    font-size: 10px;
}

li {
    background-color: beige;
    background-clip: content-box;
    padding-top: 15px;
}

</style>
