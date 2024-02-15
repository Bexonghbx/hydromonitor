<template>
    <v-container fluid="true" color="surface">
        <v-row style="max-width: 1200px, padding: 1;">
            <v-col>
                <v-sheet style="padding: 2px, height: 250px;" >
                    <paragraph>Enter date range for Analysis</paragraph>
                    <v-divider></v-divider>
                    <v-text-field v-model="start" label="Start Date" type="Date" density="compact" variant="solo-inverted" style="margin-right: 5px, max-width: 300px;" flat="true"></v-text-field>
                    <v-text-field v-model="end" label="End Date" type="Date" density="compact" variant="solo-inverted" style="max-width: 300px;" flat="true"></v-text-field>
                    <v-spacer></v-spacer>
                    <v-btn text="Analyze" color="primary" variant="tonal"></v-btn>
                </v-sheet>
            </v-col>
            <v-col cols=3 align="center">
                <v-card title="Temperature" style="width: 250px;" class="d-flex flex-row justify-center" variant="outlined" color="primary" density="compact" rounded="lg">
                    <v-card-item style="margin-bottom: -5px;">
                        <v-chip-group color="primaryContainer" variant="flat">
                            <v-tooltip text="Min" location="start">
                                <v-chip>{{ temperature.min }}</v-chip>
                            </v-tooltip>

                            <v-tooltip text="Range" location="top">
                                <v-chip>{{ temperature.range }}</v-chip>
                            </v-tooltip>

                            <v-tooltip text="Max" location="end">
                                <v-chip>{{ temperature.max }}</v-chip>
                            </v-tooltip>

                    </v-chip-group>
                    </v-card-item>

                    <v-card-item>
                        <span class="text-h1 text-primary font-weight-bold">{{ temperature.avg }}</span>
                    </v-card-item>
                </v-card>
            </v-col>
            <v-col cols=3 align="center">
                <v-card title="Humidity" style="width: 250px;" class="d-flex flex-row justify-center" variant="outlined" color="primary" density="compact" rounded="lg">
                    <v-card-item style="margin-bottom: -5px;">
                        <v-chip-group color="primaryContainer" variant="flat">
                            <v-tooltip text="Min" location="start">
                                <v-chip>{{ humidity.min }}</v-chip>
                            </v-tooltip>

                            <v-tooltip text="Range" location="top">
                                <v-chip>{{ humidity.range }}</v-chip>
                            </v-tooltip>

                            <v-tooltip text="Max" location="end">
                                <v-chip>{{ humidity.max }}</v-chip>
                            </v-tooltip>

                    </v-chip-group>
                    </v-card-item>

                    <v-card-item>
                        <span class="text-h1 text-primary font-weight-bold">{{ humidity.avg }}</span>
                    </v-card-item>
                </v-card>
            </v-col>
        </v-row>
        <v-row style="max-width: 1200px;">
            <v-col cols=12>
                <figure class="highcharts-figure">
                    <div id="container">

                    </div>
                </figure>
            </v-col>
            <v-col cols=12>
                <figure class="highcharts-figure">
                    <div id="container0">

                    </div>
                </figure>
            </v-col>
        </v-row>
        <v-row style="max-width: 1200px;">
            <v-col cols=12 style="border: 10px;">
                <figure class="highcharts-figure">
                    <div id="container1">

                    </div>
                </figure>
            </v-col>

            <v-col cols=12>
                <figure class="highcharts-figure">
                    <div id="container2">

                    </div>
                </figure>
            </v-col>
            <v-col cols=12>
                <figure class="highcharts-figure">
                    <div id="container3">

                    </div>
                </figure>
            </v-col>
        </v-row>
    </v-container>
</template>

<script setup>
/** JAVASCRIPT HERE */

// IMPORTS
import { ref,reactive,watch ,onMounted,onBeforeUnmount,computed } from "vue";  
import { useRoute ,useRouter } from "vue-router";
import { useAppStore } from "@/store/appStore";

 
// VARIABLES
const router      = useRouter();
const route       = useRoute();  
const AppStore = useAppStore();

// FUNCTIONS
onMounted(()=>{
    // THIS FUNCTION IS CALLED AFTER THIS COMPONENT HAS BEEN MOUNTED
    CreateCharts();
    Mqtt.connect(); // Connect to Broker located on the backend
    setTimeout( ()=>{
    // Subscribe to each topic
    Mqtt.subscribe("ht_status");
    Mqtt.subscribe("a");
    },3000);
    
});


onBeforeUnmount(()=>{
    // THIS FUNCTION IS CALLED RIGHT BEFORE THIS COMPONENT IS UNMOUNTED
});

start = ref({"min":0,"max":0,"avg":0,"range":0});
end   = ref({"min":0,"max":0,"avg":0,"range":0});

</script>


<style scoped>
/** CSS STYLE HERE */


</style>
  