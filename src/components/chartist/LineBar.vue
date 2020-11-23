<template>
    <div ref="chartNode" />
</template>

<script>
// Using Chartist library to draw the LineBar
// This component will accept both "data" and "options" property
// As seen in their doc: https://gionkunz.github.io/chartist-js/examples.html (first example)

import Chartist from "chartist";

export default {
    name: "LineBar",
    props: ["data", "options"],
    mounted: function() {
        // On mount we ask if the props have been instantiated and if so, we use a ref to locate the element above and draw a LineBar
        if(this.data && this.options) {
            const divNode = this.$refs.chartNode;

            this.chartInstance = new Chartist.Line(divNode, this.data, this.options);
        }
    },
    watch: {
        // Since Vue is reactive, we have to watch for new changes on data and options and update the LineBar for this
        data(newData) {
            console.log(newData);
            this.chartInstance.update(newData, this.options);
        },

        options(newOpts) {
            console.log(newOpts);
            this.chartInstance.update(this.data, newOpts);
        }
    },
}
</script>