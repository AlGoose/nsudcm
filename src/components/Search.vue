<template>
<div class="myWrap">
  <div class="tags">
    <v-combobox v-model="tags" label="Tags" chips clearable solo multiple>
      <template v-slot:selection="data">
        <v-chip :selected="data.selected" color="light-green" text-color="white" close @input="remove(data.item)">
          <strong>{{ data.item }}</strong>&nbsp;
        </v-chip>
      </template>
    </v-combobox>
    <v-btn class="button" depressed color="success" @click="search">Search</v-btn>
  </div>

  <div v-if="foundInstances.length > 0" class="searchResult">

    <vuescroll :ops="ops">

      <v-flex v-for="(item, index) in foundInstances" :key="index" xs12 class="card">
        <v-card flat color="#4ab14f" class="white--text">
          <v-layout>
            <v-flex xs12 class="userCard">
              <v-card-title primary-title>
                <div>
                  <div class="headline">{{item.instanceID}}</div>
                  <!-- <div>{{item.position}}</div> -->
                  <!-- <div>{{item.email}}</div> -->
                  <v-chip disabled v-for="item in item.tags" :key="item.id" color="light-green lighten-5" text-color="black">{{ item }}</v-chip>

                </div>
              </v-card-title>
            </v-flex>
          </v-layout>
        </v-card>
      </v-flex>

    </vuescroll>
  </div>
  <div v-else>
    <blockquote class="blockquote"> Ничего не найдено! </blockquote>
  </div>

</div>
</template>

<script>
import vuescroll from 'vuescroll'
import axios from 'axios'

export default {
  components: {
    vuescroll
  },
  data() {
    return {
      tags: [],
      selectedFile: null,
      foundInstances: [],
      foundSimplifiedTags: [],
      ops: {
        vuescroll: {
          mode: 'native',
          sizeStrategy: 'number',
          detectResize: true
        },
        scrollPanel: {},
        rail: {},
        bar: {
          keepShow: true
        }
      }
    }
  },

  methods: {
    remove(item) {
      this.tags.splice(this.tags.indexOf(item), 1)
      this.tags = [...this.tags]
    },

    search() {
      let self = this;
      axios
        .post('http://localhost:2019/api/instances/tags', self.tags)
        .then(function(res) {
          self.foundInstances = res.data;
        })
        .catch(function(err) {
          // eslint-disable-next-line
          console.log(err.message);
        });
    }
  }
}
</script>

<style scoped>
.searchResult {
  padding: 0.5%;
  position: relative;
  width: 50%;
  height: 100%;
  margin-left: 25%;
  margin-top: 1%;
  border: 1px solid grey;
  border-radius: 10px;
}

.wrap {
  width: 100%;
  height: 100%;
}

.tags {
  position: relative;
  width: 50%;
  margin-left: 25%;
  margin-top: 1%;
}

.button {
  width: 100%;
  margin-left: 0;
  padding-left: 0;
  color: white;
}

.card {
  width: 95%;
  margin: auto;
  margin-bottom: 2%;
  margin-top: 2%;
}

.blockquote {
  width: 13.5%;
  height: 13.5%;
  margin-top: 5%;
  margin-left: 43%;
  border: 1px solid red;
  border-radius: 20px;
}
</style>
