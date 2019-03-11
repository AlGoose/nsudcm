<template>
<div class="myWrap">
  <div class="content">
    <div class="one">
      <vuescroll :ops="ops">

        <v-flex v-for="(item, index) in 10" :key="index" xs12 class="card">
          <v-card color="cyan darken-2" class="white--text">
            <v-layout>
              <v-flex xs5>
                <v-img src="https://cdn.vuetifyjs.com/images/cards/foster.jpg" height="125px" contain></v-img>
              </v-flex>
              <v-flex xs7>
                <v-card-title primary-title>
                  <div>
                    <div class="headline">Supermodel</div>
                    <div>Foster the People</div>
                    <div>(2014)</div>
                  </div>
                </v-card-title>
              </v-flex>
              <v-checkbox class="myCheck" dark color="white" light v-model="selected" label="John" value="John"></v-checkbox>

            </v-layout>

          </v-card>
        </v-flex>

      </vuescroll>
    </div>

    <div class="two">
      <vuescroll :ops="ops">

        <v-flex v-for="i in 10" :key="`${i}`" xs12 class="card">
          <v-card color="cyan darken-2" class="white--text">
            <v-layout>
              <v-flex xs5>
                <v-img src="https://cdn.vuetifyjs.com/images/cards/foster.jpg" height="125px" contain></v-img>
              </v-flex>
              <v-flex xs7>
                <v-card-title primary-title>
                  <div>
                    <div class="headline">Supermodel</div>
                    <div>Foster the People</div>
                    <div>(2014)</div>
                  </div>
                </v-card-title>
              </v-flex>
            </v-layout>
          </v-card>
        </v-flex>

      </vuescroll>
    </div>
  </div>

  <!-- <v-btn depressed color="success" block>Explore</v-btn> -->
  <div class="text-xs-center">
    <v-dialog v-model="dialog" width="500">
      <template v-slot:activator="{ on }">
        <v-btn depressed color="success" block dark v-on="on">
          Expolore
        </v-btn>
      </template>

      <v-card>
        <v-card-title class="headline grey lighten-2" primary-title>
          Privacy Policy
        </v-card-title>

        <v-card-text>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis
          aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" flat @click="dialog = false">
            I accept
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</div>
</template>

<script>
import vuescroll from 'vuescroll'

export default {
  components: {
    vuescroll
  },
  data() {
    return {
      dialog: false,
      ops: {
        vuescroll: {
          mode: 'native',
          sizeStrategy: 'percent',
          detectResize: true
        },
        scrollPanel: {},
        rail: {},
        bar: {
          keepShow: true
        }
      },
      selected: []
    }
  },
}
</script>

<style>
.myWrap {
  width: 100%;
  height: 100%;
  border: 3px dashed #645a4e;
}

.content {
  background: black;
  position: relative;
  height: 95%;
}

.one {
  background: yellow;
  position: absolute;
  top: 0;
  right: 0;
  width: 50%;
  height: 100%;
}

.two {
  background: blue;
  position: absolute;
  top: 0;
  left: 0;
  width: 50%;
  height: 100%;
}

.card {
  width: 90%;
  margin: auto;
  margin-bottom: 2%;
  margin-top: 2%;
}

.myCheck {
  margin-right: 5%;
  margin-top: 10%;
}
</style>
