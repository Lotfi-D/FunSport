<template>
  <div class="m-3 card" style="max-height: 40rem;max-width: 50rem">
    <div class="card-header d-flex justify-content-between">
      <h3 v-if="info[0]" class="text-dark text-center">
        {{ info[0].match_hometeam_name }} vs {{ info[0].match_awayteam_name }}
      </h3>
    </div>
    <h3 class="card-header text-center text-dark">Match</h3>
    <div class="card-body m-0 p-0 w-100 overflow-auto text-dark">
      <div v-for="item in info" :key="item.id">
        <p class="text-center font-weight-bold">{{ item.match_round }}</p>
        <p class="text-center">
          <router-link v-bind:to="'/football/' + item.match_id">
            <button class="btn btn-success justify-content-center btnADD">
              Pronostics
            </button>
          </router-link>
        </p>
        <p class="text-center mt-3 mb-5">
          {{ item.match_date | moment("MMMM Do YYYY") }}at
          {{ item.match_time | moment("h:mm:ss") }}
        </p>
        <div class="row d-flex justify-content-center p-0 m-0">
          <div class="col-6">
            <p class="font-weight-bold text-center">
              {{ item.match_hometeam_name }}
            </p>
            <p :src="return_Score(item)" class="font-weight-bold text-center">
              <img :src="return_Link_Home(item)" alt="no Team badge" style="max-width: 4rem" />
            </p>
            <p class="text text-center">{{ item.match_hometeam_score }}</p>
            <div v-for="goal in item.goalscorer" :key="goal.id">
              <div v-if="(goal.home_scorer = goal.home_scorer)">
                {{ goal.time }}' {{ goal.home_scorer }}
                <img src="../assets/ballon.png" style="width: 1.5rem; height: 1.5rem" class="mr-1" />
              </div>
            </div>
          </div>
          <div class="col-6">
            <p class="font-weight-bold text-center">
              {{ item.match_awayteam_name }}
            </p>
            <p class="font-weight-bold text-center">
              <img :src="return_Link_Away(item)" alt="no Team badge" style="max-width: 4rem" />
            </p>
            <p class="text text-center">{{ item.match_awayteam_score }}</p>
            <div v-for="goal in item.goalscorer" :key="goal.id">
              <div v-if="(goal.away_scorer = goal.away_scorer)">
                <img src="../assets/ballon.png" style="width: 1.5rem; height: 1.5rem" class="mr-1" />
                {{ goal.away_scorer }} {{ goal.time }}'
              </div>
            </div>
          </div>
        </div>
        <div class="row d-flex justify-content-center p-0 m-0">
          <div class="col-6">
            <p class="font-weight-bold">Starting players</p>
            <div v-for="play in item.lineup.home.starting_lineups" :key="play.id">
              {{ play.lineup_number }} {{ play.lineup_player }}
            </div>
            <p class="font-weight-bold mt-5">Substitutes players</p>
            <div v-for="sub in item.lineup.home.substitutes" :key="sub.id">
              {{ sub.lineup_number }} {{ sub.lineup_player }}
            </div>
          </div>
          <div class="col-6">
            <p class="font-weight-bold">Starting players</p>
            <div v-for="play in item.lineup.away.starting_lineups" :key="play.id">
              {{ play.lineup_number }} {{ play.lineup_player }}
            </div>
            <p class="font-weight-bold mt-5">Substitutes players</p>
            <div v-for="sub in item.lineup.away.substitutes" :key="sub.id">
              {{ sub.lineup_number }} {{ sub.lineup_player }}
            </div>
          </div>
        </div>
      </div>
    </div>
    <button class="btn btn-danger p-1" style="width: 100%" @click="delToMyFavorites()">
      Unfav
    </button>
  </div>
</template>

<script>
  import ENV from "../../env.config";
  /**
   * Component card for display all teams
   * @displayName FootballMatchFavorite
   */
  export default {
    name: "FootballMatchFavorite",
    data() {
      return {
        info: [],
      };
    },
    props: {
      /**
       * The id of the match
       */
      match_id: String,
      /**
       * The id of this card
       */
      id: "",
    },
    async mounted() {
      var requestOptions = {
        method: "GET",
        redirect: "follow",
      };
      await fetch(
          "https://apiv2.apifootball.com/?action=get_events&APIkey=" +
          ENV.API_FOOTBALL +
          "&match_id=" +
          this.match_id,
          requestOptions
        )
        .then((response) => response.json())
        .then((result) => (this.info = result));
    },
    methods: {
      /**
       * Remove this team from my favorites
       *
       * @public
       */
      delToMyFavorites() {
        this.$emit("delfavorite", this.id);
      },
      /**
       * Return link to img for display badge home team in card
       *
       * @public
       */
      return_Link_Home(item) {
        return item.team_home_badge;
      },
      /**
       * Return link to img for display badge away team in card
       *
       * @public
       */
      return_Link_Away(item) {
        return item.team_away_badge;
      },
      /**
       * Format score for better UX
       *
       * @public
       */
      return_Score(item) {
        if (item.match_hometeam_score == "") {
          item.match_hometeam_score = "-";
        }

        if (item.match_awayteam_score == "") {
          item.match_awayteam_score = "-";
        }
      },
    },
  };
</script>

<style>
  .TEST {
    position: static;
    margin-top: 150px;
  }

  .btnADD {
    background: #2cf956;
    background-image: -webkit-linear-gradient(top, #2cf956, #06d530);
    background-image: -moz-linear-gradient(top, #2cf956, #06d530);
    background-image: -ms-linear-gradient(top, #2cf956, #06d530);
    background-image: -o-linear-gradient(top, #2cf956, #06d530);
    background-image: -webkit-gradient(to bottom, #2cf956, #06d530);
    -webkit-border-radius: 20px;
    -moz-border-radius: 20px;
    border-radius: 20px;
    color: #000000;
    font-family: Verdana;
    font-size: 11px;
    padding: 11px;
    -webkit-box-shadow: 1px 1px 20px 0 #24c691;
    -moz-box-shadow: 1px 1px 20px 0 #24c691;
    box-shadow: 1px 1px 20px 0 #24c691;
    text-shadow: 1px 1px 20px #ffffff;
    border: solid #ffffff 1px;
    text-decoration: none;
    display: inline-block;
    cursor: pointer;
    text-align: center;
  }
</style>