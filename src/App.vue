<template>
  <v-app>
    <v-app-bar app flat color="rgba(0, 0, 0, 0)">
      <v-app-bar-nav-icon
        color="white"
        @click="drawer = !drawer"
      ></v-app-bar-nav-icon>

      <v-toolbar-title class="title white--text pl-0">
        Messages
      </v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn color="white" icon>
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" absolute temporary>
      <v-list nav dense>
        <v-list-item-group
          v-model="group"
          active-class="deep-purple--text text--accent-4"
        >
          <v-list-item>
            <v-list-item-title>Change Units</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-title>Add City</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-title>About</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-title>Contact</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>

    <v-main style="margin-top: -57px">
      <v-carousel v-model="model" height="100vh" :show-arrows="false">
        <v-carousel-item v-for="city in cities" :key="city">
          <v-sheet :color="getRandomColour()" height="100%" tile>
            <v-row class="fill-height" align="center" justify="center">
              <div class="d-flex flex-column">
                <v-card-text>
                  <h1 class="text-h3" align="center">{{ curCity }}</h1>
                  <p class="text-h6" align="center">{{ curTitle }}</p>
                  <div>
                    <img :src="curIcon" alt="" />
                  </div>
                  <p class="text-h1" align="center">{{ curTemp }}</p>
                </v-card-text>
              </div>
            </v-row>
          </v-sheet>
        </v-carousel-item>
      </v-carousel>
    </v-main>
  </v-app>
</template>

<script>
import gql from "graphql-tag";

export default {
  name: "App",

  components: {},

  mounted() {
    this.fetchCities();
  },

  data: () => ({
    model: 0,
    cities: ["Barrie"],
    drawer: false,
    group: null,
    weather: {},
  }),

  computed: {
    curCity() {
      return this.weather?.name;
    },

    curTitle() {
      return this.weather?.weather?.summary?.title;
    },

    curTemp() {
      const actual = this.weather?.weather?.temperature?.actual;
      return actual ? Math.round(actual) : "-";
    },

    curIcon() {
      const icon = this.weather?.weather?.summary?.icon;
      return icon ? `https://openweathermap.org/img/wn/${icon}@4x.png` : "";
    },
  },

  methods: {
    fetchCities() {
      this.cities = [
        "Barrie",
        "Montego Bay",
        "London",
        "Antarctica",
        "Jerusalem",
      ];
    },

    randomIntGenerator(max = 156) {
      return Math.floor(Math.random() * Math.floor(max));
    },

    getRandomColour() {
      return `rgb(${this.randomIntGenerator()}, ${this.randomIntGenerator()}, ${this.randomIntGenerator()})`;
    },
  },

  apollo: {
    weather: {
      query: gql`
        query getWeather($city: String!) {
          weather: getCityByName(name: $city, config: { units: metric }) {
            name
            weather {
              summary {
                title
                icon
              }
              temperature {
                actual
              }
            }
          }
        }
      `,
      variables() {
        return {
          city: this.cities[this.model],
        };
      },
    },
  },
};
</script>
