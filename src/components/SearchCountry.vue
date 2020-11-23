<template>
    <div>
        <h2 class="text-center text-secondary">Select a Country</h2>
        <!-- We put all countries as options in this Select. -->
        <select class="select" name="search-country" @change="onSelectChange($event)" v-model="selectedCountry">
           <option v-for="listValue in valuesList" :value="listValue.country" :key="listValue.country">
               {{listValue.text}}
           </option>
        </select>
        <div class="flex">
            <div class="w-1/4 p-2">
                <p class="text-center font-bold text-lg text-primary">{{ casesOnCountry.confirmed }}</p>
                <p class="text-center text-secondary">Confirmed</p>
            </div>
            <div class="w-1/4 p-2">
                <p class="text-center font-bold text-lg text-primary">{{ casesOnCountry.deaths }}</p>
                <p class="text-center text-secondary">Deaths</p>
            </div>
            <div class="w-1/4 p-2">
                <p class="text-center font-bold text-lg text-primary">{{ casesOnCountry.recovered }}</p>
                <p class="text-center text-secondary">Recovered</p>
            </div>
            <div class="w-1/4 p-2">
                <p class="text-center font-bold text-lg text-primary">{{ casesOnCountry.active }}</p>
                <p class="text-center text-secondary">Active</p>
            </div>
        </div>
        <!-- We show the LineBar if the user has selected a country, and we bind the data and options to pass as props(?) -->
        <div v-if="selectedCountry !== 'N/A'">
            <LineBar v-bind:data="historicalData.data" v-bind:options="historicalData.options" />
            <div class="flex">
                <div class="w-1/4 p-2">
                    <p class="color-1 text-center">Confirmed</p>
                </div>
                <div class="w-1/4 p-2">
                    <p class="color-2 text-center">Deaths</p>
                </div>
                <div class="w-1/4 p-2">
                    <p class="color-3 text-center">Recovered</p>
                </div>
                <div class="w-1/4 p-2">
                    <p class="color-4 text-center">Active</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import { sortBy, takeRight } from "lodash";
import LineBar from "./chartist/LineBar";
import Legend from "chartist-plugin-legend";

export default {
    name: "SearchCountry",
    components: {LineBar},
    data: function() {
        return {
            casesOnCountry: {
                confirmed: "N/A",
                deaths: "N/A",
                recovered: "N/A",
                active: "N/A",
            },
            valuesList: [{country: "N/A", text: "N/A"}], 
            selectedCountry: "N/A", 
            historicalData: {
                data: {},
                options: {},
            },
        }
    },
    async created() {
        // On creation, fetch all countries to display in the Select
        const { data } = await axios.get("https://api.covid19api.com/countries");
        const allCountries = data.map(c => {
            return {
                country: c.Slug,
                text: c.Country
            }
        });

        // And append a default value called N/A, and we sort the countries alphabetically using sortBy
        this.valuesList = [{country: "N/A", text: "N/A"}, ...sortBy(allCountries, ["text"])];

    },
    methods: {
        async onSelectChange(event) {
            // This method will be called whenever the Select value changes
            try {
                const country = event.target.value;
                if(!country) return;

                // We fetch the country's particular data
                const { data } = await axios.get(`https://api.covid19api.com/total/country/${event.target.value}`);

                // Show totals for the country selected
                this.showTotals(data);

                // We split the data up to 14 items starting from the right (the last 14 days, you can change the number to include more days)
                const dataSplit = takeRight(data, 14);

                // Now we build the LineBar and show it
                this.buildLineBar(data.length, dataSplit);
            }
            catch(err) {
                console.log(err);
            }
        },
        showTotals(data) {
            // We use totals as the last element of the data (each element from data is a day starting from Day 0 till today, so today must be the totals)
            const totals = data[data.length - 1];

            this.casesOnCountry = {
                confirmed: totals.Confirmed,
                deaths: totals.Deaths,
                recovered: totals.Recovered,
                active: totals.Active,
            };
        },
        buildLineBar(length, data) {
            // We performs some maps to transform the data so it can be readable by ChartistJS
            const seriesConfirmed = data.map(c => c.Confirmed);
            const seriesDeaths = data.map(c => c.Deaths);
            const seriesRecovered = data.map(c => c.Recovered);
            const seriesActive = data.map(c => c.Active);
            const labels = data.map((c, idx) => `Day: ${length - data.length + idx}`);

            // We pass the props(?)
            this.historicalData.data = {
                labels: labels,
                series: [seriesConfirmed, seriesDeaths, seriesRecovered, seriesActive]
            };
            this.historicalData.options = {
                height: "400px",
                fullWidth: true,
                chartPadding: {
                    right: 40
                },
                stackBars: true,
                plugins: [
                    new Legend({
                        legendNames: ["Confirmed", "Deaths", "Recovered", "Active"]}
                    )
                ]
            };
        }
    }
}
</script>

<style scoped>
    p {
        margin: 0;
        padding: 0;
    }

    .flex {
        width: 100%;
        display: flex;
    }

    .w-1\/4 {
        width: 25%;
    }

    .p-2 {
        padding: 2rem;
    }

    .select {
        width: 100%;
        padding: 1rem 0.5rem;
    }

    .text-center {
        text-align: center;
    }   

    .text-lg {
        font-size: 1.5rem;
    }     

    .font-bold {
        font-weight: bold;
    }

    .text-secondary {
        color: #555B5E;
    }

    .text-primary {
        color: #5349DA;
    }

    .color-1 {
        border: 1px solid #d70206;
        color: #d70206;
    }
    
    .color-2 {
        border: 1px solid #f05b4f;
        color: #f05b4f;
    }

    .color-3 {
        border: 1px solid #f4c63d;
        color: #f4c63d;
    }

    .color-4 {
        border: 1px solid #d17905;
        color: #d17905;
    }
</style>