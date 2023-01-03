<template>
  <div class="home">
    <h1>Basket Ball Stats</h1>
    <br />
    <div class="container">
      <select
        class="form-select container w-25 px-3 py-1 shadow-sm"
        @change="loadData()"
        v-model="selectedValue"
      >
        <option value="0">Select</option>
        <option value="1">Players</option>
        <option value="2">Teams</option></select
      ><br /><br />
      <table
        class="table table-hover table-bordered table-sm"
        v-if="selectedValue == 1"
      >
        <thead class="table table-dark">
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Team</th>
            <th>Conference</th>
            <th>Info</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="p in listOfPlayers" :key="p.id">
            <td>{{ p.id }}</td>
            <td>{{ p.first_name }} {{ p.last_name }}</td>
            <td>{{ p.team.abbreviation }}</td>
            <td>{{ p.team.conference }}</td>
            <td>
              <button
                data-bs-toggle="modal"
                data-bs-target="#playerModal"
                class="btn btn-outline-dark btn-sm"
                @click="loadPlayerInfo(p)"
              >
                <i class="bi bi-info-lg" />
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <table
        class="table table-hover table-bordered table-sm"
        v-else-if="selectedValue == 2"
      >
        <thead class="table-dark">
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Conference</th>
            <th>Info</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="t in listOfTeams" :key="t.id">
            <td>{{ t.id }}</td>
            <td>{{ t.full_name }}</td>
            <td>{{ t.conference }}</td>
            <td>
              <button
                data-bs-toggle="modal"
                data-bs-target="#teamModal"
                @click="loadTeamInfo(t)"
                class="btn btn-outline-dark btn-sm"
              >
                <i class="bi bi-info-lg" />
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <div v-if="selectedValue > 0 && selectedValue < 2" class="page mb-3">
        <button
          class="btn btn-outline-dark"
          @click="prevPage()"
          :disabled="pageNum == 1"
        >
          <i class="bi bi-arrow-left" />
        </button>

        <p class="mx-3 my-1" style="font-size: 25px">
          {{ pageNum }}
        </p>

        <button class="btn btn-outline-dark" @click="nextPage()">
          <i class="bi bi-arrow-right" />
        </button>
      </div>
    </div>
    <div
      class="modal fade"
      id="playerModal"
      tabindex="-1"
      aria-labelledby="modalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="modalLabel">Player Details</h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <h2>{{ playerName }} ({{ playerPosition }})</h2>
            <h6>{{ playerTeam }}</h6>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-outline-dark"
              data-bs-dismiss="modal"
            >
              Close
            </button>
          </div>
        </div>
      </div>
    </div>

    <div
      class="modal fade"
      id="teamModal"
      tabindex="-1"
      aria-labelledby="modalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="modalLabel">Team Details</h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <h2>{{ teamName }}</h2>
            <h5>{{ teamCity }}</h5>
            <h6>{{ teamConference }}</h6>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-outline-dark"
              data-bs-dismiss="modal"
            >
              Close
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import Axios from "axios";

const playerUrl = "https://www.balldontlie.io/api/v1/players/";
const teamUrl = "https://www.balldontlie.io/api/v1/teams/";

let selectedValue = ref(0);
let pageNum = ref(1);

let listOfPlayers = ref([]);
let playerID = ref("");
let playerName = ref("");
let playerTeam = ref("");
let playerPosition = ref("");

let listOfTeams = ref([]);
let teamID = ref();
let teamName = ref("");
let teamConference = ref("");
let teamCity = ref("");

function loadData() {
  if (selectedValue.value == 1) {
    Axios.get(playerUrl + "?page=" + pageNum.value).then((res) => {
      console.log(res.data.data);
      listOfPlayers.value = res.data.data;
    });
  } else if (selectedValue.value == 2) {
    Axios.get(teamUrl).then((res) => {
      console.log(res.data.data);
      listOfTeams.value = res.data.data;
    });
  } else if (selectedValue.value == 0) {
    listOfPlayers.value = [];
    pageNum.value = 1;
  }
}

function nextPage() {
  pageNum.value += 1;

  Axios.get(playerUrl + "?page=" + pageNum.value).then((res) => {
    listOfPlayers.value = res.data.data;
  });
}

function prevPage() {
  pageNum.value -= 1;
  Axios.get(playerUrl + "?page=" + pageNum.value).then((res) => {
    listOfPlayers.value = res.data.data;
  });
}

function loadPlayerInfo(p) {
  Axios.get(playerUrl + p.id).then((res) => {
    console.log(res);
    playerName.value = p.first_name + " " + p.last_name;
    playerID.value = p.id;
    playerTeam.value = p.team.full_name;
    playerPosition.value = p.position;
  });
}

function loadTeamInfo(t) {
  Axios.get(teamUrl + t.id).then((res) => {
    console.log(res);
    teamID.value = t.id;
    teamName.value = t.full_name;
    teamCity.value = t.city;
    teamConference.value = t.conference;
  });
}
</script>

<style>
.page {
  display: flex;
  flex-direction: row;
  justify-content: right;
}
</style>
