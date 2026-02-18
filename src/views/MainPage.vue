<template>
  <v-container class="pa-4" fluid>
    <h2 class="mb-4 text-primary">Flight Search</h2>
    <v-card class="pa-4 mb-4" elevation="1">
      <v-row>
        <v-col cols="12" md="6">
          <v-select
            color="primary"
            v-model="selectedSource"
            :items="sourceItems"
            label="From"
            variant="outlined"
            hide-details
          />
        </v-col>

        <v-col cols="12" md="6">
          <v-select
            color="primary"
            v-model="selectedDestination"
            :items="destinationItems"
            label="To"
            variant="outlined"
            :disabled="!selectedSource"
            hide-details
          />
        </v-col>

        <v-col cols="12">
          <v-btn
            block
            color="primary"
            @click="searchOutput"
            :disabled="!selectedSource || !selectedDestination"
          >
            Search
          </v-btn>
        </v-col>
      </v-row>
    </v-card>
    <div v-if="filteredFlights.length">
      <p class="mb-3">{{ filteredFlights.length }} flight(s) found</p>

      <v-card
        v-for="flight in filteredFlights"
        :key="flight.id"
        class="mb-3 pa-3"
        elevation="1"
      >
        <div class="mb-2">
          <strong>{{ flight.company }}</strong>
        </div>

        <div class="mb-1">
          {{ flight.segment[0].origin }}
          →
          {{ flight.segment[flight.segment.length - 1].destination }}
        </div>

        <div class="mb-1">Duration: {{ flight.duration }} mins</div>

        <div class="mb-2">Stops: {{ flight.segment.length - 1 }}</div>

        <v-divider class="my-2"></v-divider>

        <div
          v-for="(segment, index) in flight.segment"
          :key="index"
          class="mb-2"
        >
          <div>{{ segment.origin }} → {{ segment.destination }}</div>

          <div class="text-caption">
            {{ formatTime(segment.departureTime) }} -
            {{ formatTime(segment.arrivalTime) }}
          </div>

          <div v-if="segment.connectionDuration" class="text-caption">
            Connection: {{ segment.connectionDuration }} mins
          </div>
        </div>
      </v-card>
    </div>
    <!-- <div
      v-if="
        selectedSource && selectedDestination && filteredFlights.length === 0
      "
    >
      <p>No flights found.</p>
    </div> -->
  </v-container>
</template>

<script>
import flights from "@/JSON/flights.json";

export default {
  data: () => ({
    flightData: [],
    selectedSource: "",
    selectedDestination: "",
    filteredFlights: [],
    // expandedFlights: [],
  }),
  watch: {
    selectedSource() {
      this.selectedDestination = "";
      this.filteredFlights = [];
      // this.expandedFlights = [];
    },
    selectedDestination() {
      this.filteredFlights = [];
      // this.expandedFlights = [];
    },
  },
  computed: {
    sourceItems() {
      return this.flightData
        .map((item) => item.segment[0].origin)
        .filter((origin, index, arr) => arr.indexOf(origin) === index)
        .sort();
    },
    // destinationItems() {
    //   if (!this.selectedSource) return [];
    //   const destinations = this.flightData
    //     .filter(
    //       (flight) =>
    //         flight.segment.length &&
    //         flight.segment[0].origin === this.selectedSource,
    //     )
    //     .map((flight) => flight.segment[flight.segment.length - 1].destination)
    //     .filter((destination) => destination !== this.selectedSource);
    //   return Array.from(new Set(destinations)).sort();
    // },
    // destinationItems() {
    //   if (!this.selectedSource) return [];
    //   let destination = [];
    //   this.flightData
    //     .filter((flight) => flight.segment[0].origin === this.selectedSource)
    //     .forEach((filterFlight) => {
    //       const a =
    //         filterFlight.segment[filterFlight.segment.length - 1].destination;
    //       if (!destination.includes(a)) {
    //         destination.push(a);
    //       }
    //       // destination.push(
    //       //   filterFlight.segment[filterFlight.segment.length - 1].destination,
    //       // );
    //     });

    //   // const a = destination.find((second) => second === this.selectedSource);
    //   console.log("NOW_CHECK_TEJ", destination);
    //   return destination;
    //   // return [...new Set(destination)];
    // },
    destinationItems() {
      if (!this.selectedSource) return [];
      const uniqueDestinations = [];
      this.flightData
        .filter((flight) => flight.segment[0].origin === this.selectedSource)
        .forEach((flight) => {
          const finalDestination =
            flight.segment[flight.segment.length - 1].destination;

          if (!uniqueDestinations.includes(finalDestination)) {
            uniqueDestinations.push(finalDestination);
          }
        });
      return uniqueDestinations;
    },
  },
  mounted() {
    this.flightData = flights;
  },
  methods: {
    searchOutput() {
      this.filteredFlights = this.flightData.filter((item) => {
        const origin = item.segment[0].origin;
        const destination = item.segment[item.segment.length - 1].destination;
        return (
          origin === this.selectedSource &&
          destination === this.selectedDestination
        );
      });
      // this.expandedFlights = [];
    },
    // sortByDuration() {
    //   this.filteredFlights.sort((a, b) => a.duration - b.duration);
    // },
    formatTime(time) {
      if (!time) return "";
      const date = new Date(time);
      return date.toLocaleTimeString([], {
        hour: "2-digit",
        minute: "2-digit",
      });
    },
    // toggleDetails(flightId) {
    //   if (this.expandedFlights.includes(flightId)) {
    //     this.expandedFlights = this.expandedFlights.filter(
    //       (id) => id !== flightId,
    //     );
    //   } else {
    //     this.expandedFlights.push(flightId);
    //   }
    // },
  },
};
</script>
