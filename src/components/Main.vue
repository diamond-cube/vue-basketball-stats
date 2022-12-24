<template>
  <div class="home">
    <h1>Basket Ball Stats</h1>
    <br />
    <div class="container">
      <select
        class="form-select container w-25"
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
      <table v-else-if="selectedValue == 2">
        <tr></tr>
        <tr></tr>
      </table>
      <div v-if="selectedValue > 0" class="page mb-3">
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
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">
              Player Details
            </h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <h2>{{ playerName }}</h2>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
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

<script>
import { ref } from "vue";
import Axios from "axios";
import Swal from "sweetalert2";

export default {
  name: "Main",
  setup() {
    const playerUrl = "https://www.balldontlie.io/api/v1/players/";
    const teamUrl = "https://www.balldontlie.io/api/v1/teams";

    let selectedValue = ref(0);
    let pageNum = ref(1);

    let listOfPlayers = ref();
    let playerID = ref("");
    let playerName = ref("");
    let playerTeam = ref("");
    let playerPosition = ref("");

    let listOfTeams = ref();

    function loadData() {
      if (selectedValue.value == 1) {
        Axios.get(playerUrl + "?page=" + pageNum.value).then((res) => {
          console.log(res.data.data);
          listOfPlayers.value = res.data.data;
        });
      } else if (selectedValue.value == 2) {
        Axios.get(teamUrl).then((res) => {
          console.log(res);
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
      });
    }
    return {
      loadData,
      nextPage,
      prevPage,
      loadPlayerInfo,
      selectedValue,
      listOfPlayers,
      playerID,
      playerName,
      playerPosition,
      playerTeam,
      playerUrl,
      listOfTeams,
      pageNum,
    };
  },
};
</script>

<style>
.page {
  display: flex;
  flex-direction: row;
  justify-content: right;
}
</style>
