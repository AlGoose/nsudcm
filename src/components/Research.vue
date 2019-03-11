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

    <div class="three">
      <div class="box">
        <v-flex sm12>
          <v-text-field value="John Doe" label="Outline" outline readonly></v-text-field>
        </v-flex>

        <v-flex sm12>
          <v-text-field value="John Doe" label="Outline" outline readonly></v-text-field>
        </v-flex>

        <v-flex sm12>
          <v-text-field value="John Doe" label="Outline" outline readonly></v-text-field>
        </v-flex>

      </div>
    </div>
  </div>

  <div class="text-xs-center">
    <v-dialog v-model="dialog" width="500">
      <template v-slot:activator="{ on }">
        <v-btn depressed color="success" block dark v-on="on">
          Explore
        </v-btn>
      </template>

      <v-card>
        <v-card-title class="headline grey lighten-2" primary-title>
          Ваша ссылка
        </v-card-title>

        <v-card-text>
          [ССЫЛКА]
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" flat @click="dialog = false">
            Закрыть
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
          // keepShow: true
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
  /* border: 3px dashed #645a4e; */
}

.content {
  /* background: black; */
  position: relative;
  height: 85%;
}

.one {
  /* background: yellow; */
  position: absolute;
  top: 0;
  right: 0;
  width: 30%;
  height: 100%;
}

.two {
  /* background: blue; */
  position: absolute;
  top: 0;
  left: 0;
  width: 30%;
  height: 100%;
}

.three {
  /* background: pink; */
  position: absolute;
  top: 0;
  left: 30%;
  width: 40%;
  height: 100%;
}

.card {
  width: 90%;
  margin: auto;
  margin-bottom: 2%;
  margin-top: 2%;
}

.myCheck {
  /* margin-right: 5%; */
  margin-top: 15%;
  margin-left: 10%;
}

.box {
  margin: 1.6%;
}
</style>
