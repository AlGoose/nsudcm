<template>
<div class="myWrap">
  <div class="content">
    <div class="one">
      <vuescroll :ops="ops">

        <v-flex v-for="(item, index) in employees" :key="index" xs12 class="card">
          <v-card flat color="cyan darken-2" class="white--text">
            <v-layout>
              <v-flex xs12 class="userCard" @click="selectUser(index)">
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

      </vuescroll>
    </div>

    <div class="two">
      <vuescroll :ops="ops">

        <v-flex v-for="(item, index) in patients" :key="index" xs12 class="card">
          <v-card flat color="cyan darken-2" class="white--text" >
            <v-layout row wrap class="instanceCard">
              <v-flex xs11 @click="showInfo(index)">
                <v-card-title primary-title>
                  <div>
                    <div class="headline">{{item.data.PatientName}}</div>

                    <!-- <div>{{item.data.MainDicomTags.PatientID}}</div>
                    <div>{{item.data.MainDicomTags.PatientSex}}</div> -->
                    <!-- <div><v-checkbox dark color="white" light v-model="selected" :value="item.data.MainDicomTags.PatientID"></v-checkbox></div> -->

                  </div>
                </v-card-title>
              </v-flex>
              <v-flex xs1>
                <v-checkbox dark color="white" light v-model="selectedInstances" :value="instancesIDS[index]"></v-checkbox>
              </v-flex>
            </v-layout>
          </v-card>
        </v-flex>

      </vuescroll>
    </div>

    <div class="three">
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
  </div>

  <div class="text-xs-center">
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
import vuescroll from 'vuescroll'
import axios from 'axios'

export default {
  components: {
    vuescroll
  },
  data() {
    return {
      dialog: false,
      loading: false,
      ops: {
        vuescroll: {
          mode: 'native',
          sizeStrategy: 'percent',
          detectResize: true
        },
        scrollPanel: {
          scrollingX: false,
        },
        rail: {},
        bar: {
          keepShow: true,
        },
      },
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
      selectedUserEmail: null
    }
  },

  created() {
    var self = this;
    axios
      .get('http://localhost:8042/instances/')
      .then(function(res) {
        self.instancesIDS = res.data;
        self.instancesIDS.forEach(function(element) {
          axios
            .get('http://localhost:8042/instances/' + element + '/simplified-tags')
            .then(function(res) {
              self.patients.push(res);
            })
            .catch(function(err) {
              // eslint-disable-next-line
              console.log(err.message);
            });
        })
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
    selectUser(index) {
      document.getElementsByClassName('userCard')[this.selectedUserIndex].style.background = '';
      document.getElementsByClassName('userCard')[index].style.background = '#4ab14f';
      this.selectedUserIndex = index;
      this.selectedUserEmail = this.employees[index].email;
    },

    showInfo(index) {
      document.getElementsByClassName('instanceCard')[this.selectedInstanceIndex].style.background = '';
      document.getElementsByClassName('instanceCard')[index].style.background = '#4ab14f';
      this.selectedInstanceIndex = index;

      this.selectedPatient = {
        name: this.patients[index].data.PatientName,
        id: this.patients[index].data.PatientID,
        sex: this.patients[index].data.PatientSex,
        instanceid: this.instancesIDS[index],
      }
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

.userCard:hover, .instanceCard:hover {
  background: #4ab14f;
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
