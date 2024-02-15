<template>
    <v-container fluid="true" align="center" color="surface">
        <v-row no-gutters style="max-width: 1200px;">
            <v-col cols=9>
                <figure class="highcharts-figure">
                    <div id="container">Temperature</div>
                </figure>
            </v-col>
            <v-col cols=3>
                <v-card subtitle="Temperature" style="margin-bottom: 5px, max-width: 500px;" color="primaryContainer">
                    <v-card-item> 
                        <span class="text-h3"> {{ temperature }}</span>
                    </v-card-item>
                </v-card>
                <v-card subtitle="Heat Index (Feels like)" style="margin-bottom: 5px, max-width: 500px;" color="tertiaryContainer">
                    <v-card-item>
                         <span class="text-h3">{{ heatindex }}</span>
                    </v-card-item>
                </v-card>
                <v-card subtitle="Humidity" style="margin-bottom: 5px, max-width: 500px;" color="secondaryContainer">
                    <v-card-item>
                        <span class="text-h3">{{ humidity }} </span>
                    </v-card-item>
                </v-card>
            </v-col>
        </v-row>
        <v-row no-gutters style="max-width: 1200px;" justify="start">
            <v-col cols=9>
                <figure class="highcharts-figure">
                    <div id="container1">Humidity</div>
                </figure></v-col>
        </v-row>
    </v-container>
</template>

<script setup>
/** JAVASCRIPT HERE */

// IMPORTS
import { ref,reactive,watch ,onMounted,onBeforeUnmount,computed } from "vue";  
import { useRoute ,useRouter } from "vue-router";
 
 
// VARIABLES
const router      = useRouter();
const route       = useRoute(); 
const tempHiChart = ref(null); // Chart object 
const points = ref(10); // Specify the quantity of points to be shown on the live graph simultaneously.
const shift = ref(false); // Delete a point from the left side and append a new point to the right side of the graph.

// Highcharts, Load the exporting module and Initialize exporting module.
import Highcharts from 'highcharts';
import more from 'highcharts/highcharts-more';
import Exporting from 'highcharts/modules/exporting';
Exporting(Highcharts);
more(Highcharts);

// FUNCTIONS
onMounted(()=>{
    // THIS FUNCTION IS CALLED AFTER THIS COMPONENT HAS BEEN MOUNTED
    CreateCharts();
    Mqtt.connect(); // Connect to Broker located on the backend
    setTimeout( ()=>{
    // Subscribe to each topic
    Mqtt.subscribe("ht_status");
    Mqtt.subscribe("topic2");
    },3000);
    
});


onBeforeUnmount(()=>{
    // THIS FUNCTION IS CALLED RIGHT BEFORE THIS COMPONENT IS UNMOUNTED
    Mqtt.unsubcribeAll();
});

// WATCHERS
watch(payload,(data)=> {
    if(points.value > 0){ points.value -- ; }
    else{ shift.value = true; }

 tempHiChart.value.series[0].addPoint({y:parseFloat(data.temperature.toFixed(2)) ,x: data.timestamp * 1000 }, true, shift.value);

 tempHiChart.value.series[1].addPoint({y:parseFloat(data.heatindex.toFixed(2)) ,x: data.timestamp * 1000 }, true, shift.value);

});

const CreateCharts = async () => {
// TEMPERATURE CHART
    tempHiChart.value = Highcharts.chart('container', {
        chart: { zoomType: 'x' },
        title: { text: 'Air Temperature and Heat Index Analysis', align: 'left' },
        
        yAxis: {
            title: { text: 'Air Temperature & Heat Index' , style:{color:'#000000'}},
            labels: { format: '{value} °C' }
        },
        
        xAxis: {
            type: 'datetime',
            title: { text: 'Time', style:{color:'#000000'} },
        },

        tooltip: { shared:true, },
        series: [
        {
            name: 'Temperature',
            type: 'spline',
            data: [],
            turboThreshold: 0,
            color: Highcharts.getOptions().colors[0]
        },

        {
            name: 'Heat Index',
            type: 'spline',
            data: [],
            turboThreshold: 0,
            color: Highcharts.getOptions().colors[1]
        } ],
    });
};

// COMPUTED PROPERTIES
const temperature = computed(()=>{
    if(!!payload.value){
        return `${payload.value.temperature.toFixed(2)} °C`;
    }
});

const heatindex = computed(()=>{
    if(!!payload.value){
        return `${payload.value.heatindex.toFixed(2)} °C`;
    }
});

const humidity = computed(()=>{
    if(!!payload.value){
        return `${payload.value.humidity.toFixed(2)} °C`;
    }
});
</script>


<style scoped>
/** CSS STYLE HERE */
Figure {
border: 2px solid black;
}

</style>
  