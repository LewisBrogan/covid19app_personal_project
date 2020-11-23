<template>
    <div class="border-bottom">
        <h2 class="text-center text-secondary">COVID Total Cases</h2>
        <div class="flex">
            <div class="w-1/3 p-2">
                <p class="text-center font-bold text-lg text-primary">{{ globals.newConfirmed }}</p>
                <p class="text-center text-secondary">New Cases</p>
            </div>
            <div class="w-1/3 p-2">
                <p class="text-center font-bold text-lg text-primary">{{globals.newDeaths}}</p>
                <p class="text-center text-secondary">New Deaths</p>
            </div>
            <div class="w-1/3 p-2">
                <p class="text-center font-bold text-lg text-primary">{{globals.newRecovered}}</p>  
                <p class="text-center text-secondary">New Recovered</p>    
            </div>
        </div>
        <div class="flex">
            <div class="w-1/3 p-2">
                <p class="text-center font-bold text-lg text-primary">{{globals.totalConfirmed}}</p>
                <p class="text-center text-secondary">Total Confirmed</p>
            </div>
            <div class="w-1/3 p-2">
                <p class="text-center font-bold text-lg text-primary">{{globals.totalDeaths}}</p>
                <p class="text-center text-secondary">Total Deaths</p>
            </div>
            <div class="w-1/3 p-2">
                <p class="text-center font-bold text-lg text-primary">{{globals.totalRecovered}}</p>
                <p class="text-center text-secondary">Total Recovered</p>
            </div>
        </div>
    </div>
</template>

<script>
// We use Axios for fetching data
import axios from "axios";

export default {
    name: "Globals",
    data: function() {
        return {
            globals: {
                newConfirmed: "N/A",
                newDeaths: "N/A",
                newRecovered: "N/A",
                totalConfirmed: "N/A",
                totalDeaths: "N/A",
                totalRecovered: "N/A"
            }
        };
    },
    async created() {
        // On Vue creating component, fetch all the data we need
        const { data } = await axios.get("https://api.covid19api.com/summary");
        this.globals = {
            newConfirmed: data.Global.NewConfirmed,
            newDeaths: data.Global.NewDeaths,
            newRecovered: data.Global.NewRecovered,
            totalConfirmed: data.Global.TotalConfirmed,
            totalDeaths: data.Global.TotalDeaths,
            totalRecovered: data.Global.TotalRecovered
        };
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

    .w-1\/3 {
        width: 33.3%;
    }

    .p-2 {
        padding: 2rem;
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

    .border-bottom {
        border-bottom: 1px solid #cccccc;
    }
</style>