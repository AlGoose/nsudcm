<template>
<div class="main">
  <div class="instance-block">
    <div class="tags">
      <div id="search">
        <scrolly class="foo" :style="{ width: '100%', height: '100%'}">
          <scrolly-viewport>
            <v-combobox v-model="tags" label="Tags" chips clearable solo multiple>
              <template v-slot:selection="data">
                <v-chip :selected="data.selected" color="light-green" text-color="white" close @input="remove(data.item)">
                  <strong>{{ data.item }}</strong>&nbsp;
                </v-chip>
              </template>
            </v-combobox>
          </scrolly-viewport>
          <scrolly-bar axis="y"></scrolly-bar>
        </scrolly>
      </div>
      <div id="button">
        <v-btn id="searchButton" depressed color="success" @click="search">Search</v-btn>
      </div>
    </div>

    <scrolly class="foo" :style="{ width: '100%', height: '80%', border: '1px solid black'}">
      <scrolly-viewport>
        <v-flex v-for="(item, index) in instancesIDS" :key="index" xs12 class="card">
          <v-card flat color="cyan darken-2" class="white--text">
            <v-layout  id="card" row wrap class="instanceCard">
              <v-flex xs11 @click="showInfo(item.instanceID, index)">
                <v-card-title primary-title>
                  <div>
                    <div class="headline">{{item.instanceID}}</div>
                    <v-chip disabled v-for="item in item.tags" :key="item.id" color="light-green lighten-5" text-color="black">{{ item }}</v-chip>
                  </div>
                </v-card-title>
              </v-flex>
              <v-flex xs1>
                <v-checkbox dark color="white" light v-model="selectedInstances" :value="instancesIDS[index].instanceID"></v-checkbox>
              </v-flex>
            </v-layout>
          </v-card>
        </v-flex>
      </scrolly-viewport>
      <scrolly-bar axis="y"></scrolly-bar>
    </scrolly>
  </div>

  <div class="content-block">
    <div class="box">
      <v-flex sm12>
        <v-text-field :value="selectedPatient.name" label="Name" outline readonly></v-text-field>
      </v-flex>

      <v-flex sm12>
        <v-text-field :value="selectedPatient.id" label="ID" outline readonly></v-text-field>
      </v-flex>

      <v-flex sm12>
        <v-text-field :value="selectedPatient.sex" label="Sex" outline readonly></v-text-field>
      </v-flex>

      <v-flex sm12>
        <v-text-field :value="selectedPatient.instanceid" label="InstanceID" outline readonly></v-text-field>
      </v-flex>

      <v-flex sm12>
        <ul>
          <li v-for="(item, index) in selectedInstances" :key="index">
            {{ item }}
          </li>
        </ul>
      </v-flex>

    </div>
  </div>

  <div class="user-block">
    <scrolly class="foo" :style="{ width: '100%', height: '99%'}">
      <scrolly-viewport>
        <v-flex v-for="(item, index) in employees" :key="index" xs12 class="card">
          <v-card flat color="cyan darken-2" class="white--text">
            <v-layout>
              <v-flex xs12 class="userCard" id="card" @click="selectUser(index)">
                <v-card-title primary-title>
                  <div>
                    <div class="headline">{{item.surname + ' ' + item.name}}</div>
                    <div>{{item.position}}</div>
                    <div>{{item.email}}</div>
                  </div>
                </v-card-title>
              </v-flex>
            </v-layout>
          </v-card>
        </v-flex>
      </scrolly-viewport>
      <scrolly-bar axis="y"></scrolly-bar>
    </scrolly>
  </div>

  <div class="button-block">
    <v-dialog v-model="dialog" width="500">
      <template v-slot:activator="{ on }">
        <v-btn depressed color="success" block dark v-on="on">Explore</v-btn>
      </template>

      <v-card>
        <v-card-title class="headline grey lighten-2" primary-title>Подтверждение</v-card-title>
        <v-card-text>Вы уверены в выборе?</v-card-text>
        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn v-if="loading" color="success" flat loading></v-btn>
          <v-btn v-else color="success" flat @click="sendMail()">Отправить</v-btn>
          <v-btn color="primary" flat @click="dialog = false">Закрыть</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</div>
</template>

<script>
import axios from 'axios';
import { Scrolly, ScrollyViewport, ScrollyBar } from 'vue-scrolly';

export default {
  components: {
    Scrolly,
    ScrollyViewport,
    ScrollyBar
  },
  data() {
    return {
      dialog: false,
      loading: false,
      selectedInstances: [],
      patientsIDS: null,
      instancesIDS: null,
      patients: [],
      selectedPatient: {
        name: '',
        id: '',
        sex: '',
        instanceid: '',
      },
      employees: null,
      selectedUserIndex: 0,
      selectedInstanceIndex: 0,
      selectedUserEmail: null,
      tags: []
    }
  },

  created() {
    var self = this;
    // axios
    //   .get('http://localhost:8042/instances/')
    //   .then(function(res) {
    //     self.instancesIDS = res.data;
    //     self.instancesIDS.forEach(function(element) {
    //       axios
    //         .get('http://localhost:8042/instances/' + element + '/simplified-tags')
    //         .then(function(res) {
    //           self.patients.push(res);
    //         })
    //         .catch(function(err) {
    //           // eslint-disable-next-line
    //           console.log(err.message);
    //         });
    //     })
    //   })
    //   .catch(function(err) {
    //     // eslint-disable-next-line
    //     console.log(err.message);
    //   });
    axios
      .get('http://localhost:2019/api/instances/')
      .then(function(res) {
        // eslint-disable-next-line
        console.log(res.data);
        self.instancesIDS = res.data;
      })
      .catch(function(err) {
        // eslint-disable-next-line
        console.log(err.message);
      });

    axios
        .get('http://localhost:2019/api/employees/')
        .then(function(res) {
          self.employees = res.data;
        })
        .catch(function(err) {
          // eslint-disable-next-line
          console.log(err.message);
        });
  },

  methods: {
    search() {
      if(this.instancesIDS){
        document.getElementsByClassName('instanceCard')[this.selectedInstanceIndex].style.background = '';
        this.selectedInstanceIndex = 0;
      }

      this.selectedInstances = [];
      this.instancesIDS = [];

      let self = this;
      axios
        .post('http://localhost:2019/api/instances/tags', self.tags)
        .then(function(res) {
          // eslint-disable-next-line
          // console.log(res.data);
          self.instancesIDS = res.data;
        })
        .catch(function(err) {
          // eslint-disable-next-line
          console.log(err.message);
        });
    },

    remove(item) {
      this.tags.splice(this.tags.indexOf(item), 1)
      this.tags = [...this.tags]
    },

    selectUser(index) {
      document.getElementsByClassName('userCard')[this.selectedUserIndex].style.background = '';
      document.getElementsByClassName('userCard')[index].style.background = '#4ab14f';
      this.selectedUserIndex = index;
      this.selectedUserEmail = this.employees[index].email;
    },

    showInfo(id, index) {
      // let self = this;
      if(document.getElementsByClassName('instanceCard')){
        document.getElementsByClassName('instanceCard')[this.selectedInstanceIndex].style.background = '';
        document.getElementsByClassName('instanceCard')[index].style.background = '#4ab14f';
        this.selectedInstanceIndex = index;
      }
      // axios
      //     .get('http://localhost:8042/instances/' + id + '/simplified-tags')
      //     .then(function(res) {
      //       self.selectedPatient = {
      //         name: res.data.PatientName,
      //         id: res.data.PatientID,
      //         sex: res.data.PatientSex,
      //         instanceid: id,
      //       }
      //       console.log(res);
      //     })
      //     .catch(function(err) {
      //       // eslint-disable-next-line
      //       console.log(err.message);
      //     });
    },

    sendMail() {
      if(this.selectedInstances.length == 0 || this.selectedUserEmail == null) {
        alert('Не выбрана болезнь(-ни) или исследователь!');
        this.dialog = false;
        return;
      }

      this.loading = true;
      var self = this;
      var json = JSON.stringify(this.selectedInstances);
      let objJsonB64 = Buffer.from(json).toString("base64");

      const sgMail = require('@sendgrid/mail');
      sgMail.setApiKey(process.env.VUE_APP_SENDGRID_API_KEY);
      const msg = {
        to: this.selectedUserEmail,
        from: 'alex@nsudcm.com',
        subject: 'Research Files',
        html: '<p>Here’s an attachment for you!</p>',
        attachments: [{
          content: objJsonB64,
          filename: 'research.json',
          type: 'application/json',
          disposition: 'attachment',
          content_id: 'mytext'
        }, ],
      };

      sgMail
        .send(msg)
        .then(() => {
          // eslint-disable-next-line
          console.log("Success!");
          self.dialog = false;
          self.loading = false;
        })
        .catch(error => {
          // eslint-disable-next-line
          console.log("Failed!");
          // eslint-disable-next-line
          console.error(error.toString());
          self.loading = false;
        });
    }
  }
}
</script>

<style scoped>
html, body {margin:0;padding:0;}
.main {
    margin:0 auto;
    width:100%;
    height: 100%;
}
.main .instance-block,.content-block,.user-block  {
    display:inline-block;
    vertical-align:top;
}
.instance-block {
    /* background:red; */
    width:30%;
    height:92%;
}
.content-block {
    /* background:green; */
    width:40%;
    height:92%;
}
.user-block {
    /* background:blue; */
    width:30%;
    height:92%;
}
.button-block {
  /* background:pink; */
  width:100%;
  height:5%;
}
.card {
  width: 90%;
  margin: auto;
  margin-bottom: 2%;
  margin-top: 2%;
}
.card #card:hover{
  background: #4ab14f;
}
.tags {
  width: 100%;
  height: 20%;
  /* background: black; */
}
.tags #search {
  width: 100%;
  height: 70%;
  /* background: yellow; */
}
.tags #button {
  width: 100%;
  height: 30%;
}
#button #searchButton {
  width: 100%;
  height: 100%;
  margin: 0;
}
.box {
   margin: 2%;
}
.foo {
  border-radius: 10px;
}
</style>
