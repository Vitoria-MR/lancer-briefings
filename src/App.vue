<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "000",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "000",
          "name": "News",
          "status": ""
        },
        {
          "slug": "001",
          "name": "Op Spear Point",
          "status": "success"
        },
  
      ],
      "pilots": [
        {
          "callsign": "Banshee",
          "alias": "1° Tenente Noemi Bondevik",
          "code": "8f014e96-e7d3-4310-8086-d2702be4ff8b",
          "corpro": "Union",
          "frame": "MS-06 Zaku II",
          "mech": "Fantasma da Opera"
        },
        {
          "callsign": "Criador",
          "alias": "1° Sargento Daniel Yermakov",
          "code": "dcf6f3c0-f7f0-4edd-a853-498894374c57",
          "corpro": "Union",
          "frame": "MS-05 Zaku I",
          "mech": "G.R.I.M.M"
        },
        {
          "callsign": "Doutor",
          "alias": "1° Tenente Azeera Ustra",
          "code": "f145e1e8-9e78-4d88-85e8-52098a84883e",
          "corpro": "Union",
          "frame": "MS-05 Zaku I",
          "mech": "Ruprest" 
        },
        {
          "callsign": "Pragah",
          "alias": "3° Sargento Cléber afonso IV",
          "code": "0bc47d24-29e9-4c53-8666-d5602a6d7032",
          "corpro": "Union",
          "frame": "MS-05 Zaku I",
          "mech": "D4C"
        },
        {
          "callsign": "HYBRIS",
          "alias": "■■■■■■■■■■■■■■■■",
          "code": "■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■",
          "corpro": "■■■■■■■■",
          "frame": "■■■■■■■■■■■■■■",
          "mech": "Absolute Solver"
        },

      ],
      "header": {
        "planet": "Ceres",
        "year": "october U.C. 0078",
        "system": "Solar",
        "gate": "■■■■■",
        "ring": "■■■■■■■■■■■",
        "headerTitle": "U.M.A.F.",
        "headerSubtitle": "Achiles Project",
        "subheaderTitle": "Special Operations",
        "subheaderSubtitle": "Nu-Epsilon-Omicron-Sigma",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
