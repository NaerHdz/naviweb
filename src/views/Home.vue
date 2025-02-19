<template>
  <div class="home">
    <Header />
    <v-main>
      <v-card style="border-radius: 20px" color="red accent-4">
        <v-img src="../assets/cover-page.webp">
          <Carousel class="mt-10" />
        </v-img>
      </v-card>

      <h1 style="color: black">Juegos destacados de la semana</h1>
      <v-container fluid>
        <v-data-iterator
          :items="items"
          :items-per-page.sync="itemsPerPage"
          :page.sync="page"
          :search="search"
          :sort-by="sortBy.toLowerCase()"
          :sort-desc="sortDesc"
          hide-default-footer
        >
          <template v-slot:header>
            <v-toolbar dark color="red accent-4" class="mb-1">
              <v-text-field
                v-model="search"
                clearable
                flat
                solo-inverted
                rounded
                hide-details
                prepend-inner-icon="mdi-magnify"
                label="Search"
              ></v-text-field>
              <template v-if="$vuetify.breakpoint.mdAndUp">
                <v-spacer></v-spacer>
                <v-select
                  v-model="sortBy"
                  flat
                  solo-inverted
                  hide-details
                  :items="keys"
                  prepend-inner-icon="mdi-magnify"
                  label="Sort by"
                ></v-select>
                <v-spacer></v-spacer>
                <v-btn-toggle v-model="sortDesc" mandatory>
                  <v-btn large depressed color="blue" :value="false">
                    <v-icon>mdi-arrow-up</v-icon>
                  </v-btn>
                  <v-btn large depressed color="blue" :value="true">
                    <v-icon>mdi-arrow-down</v-icon>
                  </v-btn>
                </v-btn-toggle>
              </template>
            </v-toolbar>
          </template>

          <template v-slot:default="props">
            <v-row>
              <v-col
                v-for="item in props.items"
                :key="item.name"
                cols="12"
                sm="6"
                md="4"
                lg="3"
              >
                <v-card>
                  <v-card-title class="subheading font-weight-bold">
                    <v-img :src="item['imagen']"> </v-img>
                  </v-card-title>

                  <v-divider></v-divider>

                  <v-list dense>
                    <v-list-item
                      v-for="(key, index) in filteredKeys"
                      :key="index"
                    >
                      <v-list-item-content
                        :class="{ 'blue--text': sortBy === key }"
                      >
                        {{ key }}:
                      </v-list-item-content>
                      <v-list-item-content
                        class="align-end"
                        :class="{ 'blue--text': sortBy === key }"
                      >
                        <div v-if="key !== 'Desarrollador'">
                          {{ item[key.toLowerCase()] }}
                        </div>
                        <div v-else>
                          <router-link
                            style="text-decoration: none"
                            to="/Profile/NaerHdz"
                            >{{ item["desarrollador"] }}</router-link
                          >
                        </div>
                      </v-list-item-content>
                    </v-list-item>
                    <a href="https://www.snk-corp.co.jp/us/games/kof-xv/">
                      Ver detalles
                    </a>
                  </v-list>
                </v-card>
              </v-col>
            </v-row>
          </template>

          <template v-slot:footer>
            <v-row class="mt-2" align="center" justify="center">
              <span class="grey--text">Items per page</span>
              <v-menu offset-y>
                <template v-slot:activator="{ on, attrs }">
                  <v-btn
                    dark
                    text
                    color="primary"
                    class="ml-2"
                    v-bind="attrs"
                    v-on="on"
                  >
                    {{ itemsPerPage }}
                    <v-icon>mdi-chevron-down</v-icon>
                  </v-btn>
                </template>
                <v-list>
                  <v-list-item
                    v-for="(number, index) in itemsPerPageArray"
                    :key="index"
                    @click="updateItemsPerPage(number)"
                  >
                    <v-list-item-title>{{ number }}</v-list-item-title>
                  </v-list-item>
                </v-list>
              </v-menu>

              <v-spacer></v-spacer>

              <span class="mr-4 grey--text">
                Page {{ page }} of {{ numberOfPages }}
              </span>
              <v-btn
                fab
                dark
                color="blue darken-3"
                class="mr-1"
                @click="formerPage"
              >
                <v-icon>mdi-chevron-left</v-icon>
              </v-btn>
              <v-btn
                fab
                dark
                color="blue darken-3"
                class="ml-1"
                @click="nextPage"
              >
                <v-icon>mdi-chevron-right</v-icon>
              </v-btn>
            </v-row>
          </template>
        </v-data-iterator>
      </v-container>
    </v-main>
  </div>
</template>

<script>
// @ is an alias to /src
import Header from "@/components/Header";
import Footer from "@/components/Footer";
import Carousel from "@/components/Carousel";

export default {
  name: "Home",
  components: {
    Header,
    Footer,
    Carousel,
  },
  async mounted() {
    const resp = await fetch("http://localhost:4000/api/game/").then((r) =>
      r.json()
    );

    for (let i = 0; i < resp.gameDB.length; i++) {
      this.items.push({
        imagen: require(`@/assets/${resp.gameDB[i].image}`),
        nombre: resp.gameDB[i].name,
        genero: resp.gameDB[i].genre,
        desarrollador: "NaerHdz",
        fecha: resp.gameDB[i].creatAt,
      });
    }
  },
  data: () => {
    return {
      search: "",
      tab: "",
      category: ["Juegos", "Genero", "Desarrollador", "Fecha"],
      itemsPerPageArray: [4, 8, 12],
      search: "",
      filter: {},
      sortDesc: false,
      page: 1,
      itemsPerPage: 4,
      sortBy: "Nombre",
      keys: ["Nombre", "Genero", "Desarrollador", "Fecha"],
      items: [],
    };
  },
  computed: {
    numberOfPages() {
      return Math.ceil(this.items.length / this.itemsPerPage);
    },
    filteredKeys() {
      return this.keys.filter((key) => key !== "Name");
    },
  },
  methods: {
    nextPage() {
      if (this.page + 1 <= this.numberOfPages) this.page += 1;
    },
    formerPage() {
      if (this.page - 1 >= 1) this.page -= 1;
    },
    updateItemsPerPage(number) {
      this.itemsPerPage = number;
    },
  },
};
</script>
