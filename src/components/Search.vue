<template>
<div class="myWrap">
  <div class="tags">
    <v-combobox v-model="chips" label="Tags" chips clearable solo multiple>
      <template v-slot:selection="data">
        <v-chip :selected="data.selected" color="light-green" text-color="white" close @input="remove(data.item)">
          <strong>{{ data.item }}</strong>&nbsp;
        </v-chip>
      </template>
    </v-combobox>
    <v-btn class="button" depressed color="success" @click="search">Search</v-btn>
  </div>

  <div class="searchResult">
    <vuescroll :ops="ops">
      <p v-for="item in chips" :key="item.id" color="light-green" text-color="white">{{ item }}</p>
      <!-- <v-chip v-for="item in chips" :key="item.id" color="light-green" text-color="white">{{ item }}</v-chip> -->
    </vuescroll>
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
      chips: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
      selectedFile: null,
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
      this.chips.splice(this.chips.indexOf(item), 1)
      this.chips = [...this.chips]
    },

    search() {
      axios
        .get('http://localhost:8042/patients/')
        .then(function(res) {
          console.log(res);
        })
        .catch(function(err) {
          console.log(err.message);
        });
    }
  }
}
</script>

<style scoped>
.searchResult {
  position: relative;
  width: 50%;
  height: 50%;
  margin-left: 25%;
  /* background: yellow; */
}

.wrap {
  width: 100%;
  height: 100%;
  /* background: grey; */
  /* border: 3px dashed #645a4e; */
}

.tags {
  position: relative;
  width: 50%;
  margin-left: 25%;
  margin-top: 1%;
  /* background: red; */
}

li {
  list-style-type: none;
}

ul {
  margin-left: 0;
  padding-left: 0;
}

.button {
  width: 100%;
  margin-left: 0;
  padding-left: 0;
  color: white;
}
</style>
