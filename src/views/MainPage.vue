<template>
  <v-container class="pa-4" fluid>
    <div class="text-h6 font-weight-bold text-primary mb-3">Flight Search</div>

    <v-card class="pa-5 mb-5 rounded-xl" elevation="2">
      <v-row dense>
        <v-col cols="12">
          <v-select
            v-model="selectedSource"
            :items="sourceItems"
            label="From"
            variant="outlined"
            density="comfortable"
            class="mb-2"
            hide-details="auto"
            bg-color="background"
            color="primary"
          />
        </v-col>

        <v-col cols="12">
          <v-select
            v-model="selectedDestination"
            :items="destinationItems"
            label="To"
            variant="outlined"
            density="comfortable"
            class="mb-3"
            :disabled="!selectedSource"
            hide-details="auto"
            bg-color="background"
            color="primary"
          />
        </v-col>

        <v-col cols="12">
          <v-btn
            block
            color="primary"
            size="large"
            @click="searchOutput"
            :disabled="!selectedSource || !selectedDestination"
            class="mt-1"
            variant="elevated"
          >
            Search Flights
          </v-btn>
        </v-col>
      </v-row>
    </v-card>

    <div
      v-if="filteredFlights.length"
      class="d-flex align-center justify-space-between mb-4"
    >
      <div class="text-body-2 text-medium-emphasis">
        {{ filteredFlights.length }} flight{{
          filteredFlights.length > 1 ? "s" : ""
        }}
        found
      </div>
    </div>

    <div v-for="flight in filteredFlights" :key="flight.id" class="mb-4">
      <v-card class="rounded-xl" elevation="2">
        <v-card-item>
          <template v-slot:prepend>
            <v-avatar
              color="primary"
              variant="tonal"
              size="40"
              class="rounded-lg"
            >
              <span class="text-primary font-weight-bold">{{
                flight.company[0]
              }}</span>
            </v-avatar>
          </template>

          <v-card-title class="font-weight-bold text-body-1">
            {{ flight.company }}
          </v-card-title>

          <v-card-subtitle class="text-body-2 mt-1">
            <v-icon size="14" color="primary" class="mr-1"
              >mdi-airplane-takeoff</v-icon
            >
            {{ flight.segment[0].origin }}
            <v-icon size="14" color="medium-emphasis" class="mx-1"
              >mdi-arrow-right</v-icon
            >
            {{ flight.segment[flight.segment.length - 1].destination }}
          </v-card-subtitle>

          <template v-slot:append>
            <v-chip
              color="primary"
              variant="tonal"
              size="small"
              class="font-weight-bold"
            >
              {{ flight.points }} pts
            </v-chip>
          </template>
        </v-card-item>

        <v-divider></v-divider>

        <v-card-text>
          <v-row align="center" class="mb-2">
            <v-col cols="auto" class="pr-0">
              <v-icon size="16" color="medium-emphasis"
                >mdi-clock-outline</v-icon
              >
            </v-col>
            <v-col>
              <span class="text-body-2">{{ flight.duration }} mins total</span>
            </v-col>
          </v-row>

          <v-row align="center" class="mb-2">
            <v-col cols="auto" class="pr-0">
              <v-icon size="16" color="medium-emphasis">mdi-transfer</v-icon>
            </v-col>
            <v-col>
              <span class="text-body-2">
                {{ flight.segment.length - 1 }} stop{{
                  flight.segment.length > 2 ? "s" : ""
                }}
              </span>
            </v-col>
          </v-row>

          <v-card class="pa-3 mt-3" variant="outlined">
            <div
              v-for="(segment, index) in flight.segment"
              :key="index"
              class="mb-3"
            >
              <div class="text-body-2 font-weight-medium">
                {{ segment.origin }} → {{ segment.destination }}
              </div>

              <div class="text-caption text-medium-emphasis mt-1">
                {{ formatTime(segment.departureTime) }} -
                {{ formatTime(segment.arrivalTime) }}
              </div>

              <div
                v-if="segment.connectionDuration"
                class="text-caption text-warning mt-1"
              >
                <v-icon size="14" color="warning" class="mr-1">
                  mdi-timer-sand
                </v-icon>
                Connection: {{ segment.connectionDuration }} mins
              </div>

              <v-divider
                v-if="index < flight.segment.length - 1"
                class="mt-3"
              />
            </div>
          </v-card>
        </v-card-text>
      </v-card>
    </div>

    <!-- <v-card
      v-if="
        selectedSource && selectedDestination && filteredFlights.length === 0
      "
      class="rounded-xl pa-8 text-center"
      elevation="2"
    >
      <v-icon size="48" color="medium-emphasis" class="mb-3"
        >mdi-airplane-off</v-icon
      >
      <div class="text-body-1 text-medium-emphasis">No flights found</div>
      <div class="text-caption text-medium-emphasis mt-1">
        Try different airports
      </div>
    </v-card> -->
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
