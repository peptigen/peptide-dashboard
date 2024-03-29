<template>
  <div>
    <section class="hero is-primary is-bold" v-if="screen.width > 760">
      <div class="hero-body" style="padding: 0px">
        <div class="container">
          <div class="floating-hero">
            <span class="title">peptide.bio</span><br />
            <span class="subtitle"> v{{ version }} </span>
          </div>
          <sequence-viewer
            :sequence="sequence"
            :view-width="viewWidth"
            v-on:selection-update="selectedIndex = $event"
          ></sequence-viewer>
          <div class="container"></div>
        </div>
      </div>
    </section>
    <section>
      <version :version="version"></version>
    </section>
    <section>
      <div class="container">
        <div class="columns is-centered">
          <div ref="sequencecontainer" class="column">
            <sequence-input
              v-on:sequence-update="sequence = $event"
              v-on:sequence-push="pushSequence"
              :ready="resultsReady"
            >
            </sequence-input>
            <div class="block results-block" v-if="past.length > 0">
              <table class="table results-table">
                <thead>
                  <tr>
                    <td>Sequence</td>
                    <td>Hemolytic</td>
                    <td>Soluble</td>
                    <td>Nonfouling</td>
                    <td>Synthesis</td>
                  </tr>
                </thead>
                <tbody>
                  <template
                    v-for="item in past.slice().reverse()"
                    :key="item.sequence"
                  >
                    <tr>
                      <td>{{ item.sequence }}</td>
                      <td>{{ item.hemolytic }}</td>
                      <td>{{ item.soluble }}</td>
                      <td>{{ item.nonfouling }}</td>
                      <td>{{ item.synthesis }}</td>
                    </tr>
                  </template>
                </tbody>
              </table>
            </div>
            <div v-if="past.length > 0" class="control">
              <a
                id="results-button"
                class="button is-info is-small"
                :href="blob"
                download="peptide.csv"
              >
                Download
              </a>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section>
      <div class="container">
        <div class="columns">
          <div class="column card">
            <div class="card-content">
              <h3 class="title is-size-4">Hemolytic Prediction</h3>
              <h4 class="subtitle is-size-6">
                Predicted ability for peptide to lyse red blood cells
              </h4>
              <tf-prediction
                url="https://raw.githubusercontent.com/ur-whitelab/peptide-dashboard/master/models/hemo-rnn/"
                :sequence="sequence"
                adjective="hemolytic"
                v-on:hemolytic-update="hemolytic = $event"
              ></tf-prediction>
              <div class="ref-footer">
                <reference
                  reflink="https://pubs.acs.org/doi/10.1021/acs.jcim.0c00946"
                  reftitle="Investigating Active Learning and Meta-Learning for Iterative Peptide Design"
                  journal="J. Chem. Inf. Model."
                  year="2021"
                ></reference>
                <br />
                <reference
                  reflink="https://doi.org/10.1093/nar/gkaa991"
                  reftitle="DBAASP v3: database of antimicrobial/cytotoxic activity and structure of peptides as a resource for development of new therapeutics"
                  journal="Nucleic Acids Research"
                  year="2020"
                ></reference>
              </div>
            </div>
          </div>
          <div class="card column">
            <div class="card-content">
              <h3 class="title is-size-4">Solubility Prediction</h3>
              <h4 class="subtitle is-size-6">
                Predicted solubility of given sequence
              </h4>
              <tf-prediction
                url="https://raw.githubusercontent.com/ur-whitelab/peptide-dashboard/master/models/sol-rnn/"
                :sequence="sequence"
                adjective="soluble"
                v-on:soluble-update="soluble = $event"
              ></tf-prediction>
              <div class="ref-footer">
                <reference
                  reflink="https://pubs.acs.org/doi/10.1021/acs.jcim.0c00946"
                  reftitle="Investigating Active Learning and Meta-Learning for Iterative Peptide Design"
                  journal="J. Chem. Inf. Model."
                  year="2021"
                ></reference>
                <br />
                <reference
                  reflink="https://febs.onlinelibrary.wiley.com/doi/abs/10.1111/j.1742-4658.2012.08603.x"
                  reftitle="PROSO II–a new method for protein solubility prediction"
                  journal="The FEBS journal "
                  year="2012"
                ></reference>
              </div>
            </div>
          </div>
        </div>
        <div class="columns">
          <div class="card column">
            <div class="card-content">
              <h3 class="title is-size-4">Nonfouling Prediction</h3>
              <h4 class="subtitle is-size-6">
                Predicted ability to resist non-specific interactions
              </h4>
              <p class="card-header-subtitle is-size-5 is-spaced"></p>
              <tf-prediction
                url="https://raw.githubusercontent.com/ur-whitelab/peptide-dashboard/master/models/human-rnn/"
                :sequence="sequence"
                adjective="nonfouling"
                v-on:nonfouling-update="nonfouling = $event"
              ></tf-prediction>
              <div class="ref-footer">
                <reference
                  reflink="https://onlinelibrary.wiley.com/doi/abs/10.1002/pep2.24079"
                  reftitle="Classifying antimicrobial and multifunctional peptides with Bayesian network models"
                  journal="Pep. Sci."
                  year="2018"
                ></reference>
                <br />
                <reference
                  reflink="https://doi.org/10.1039/C2SC21135A"
                  reftitle="Decoding nonspecific interactions from nature"
                  journal="Chem. Sci."
                  year="2012"
                ></reference>
              </div>
            </div>
          </div>
          <div class="card column">
            <header class="card-header">
              <h3 class="card-header-title is-size-4 is-spaced bd-anchor-title">
                Milton Coupling Efficiency
              </h3>
              <a href="#" class="card-header-icon" aria-label="more options">
                <span class="icon">
                  <i class="fa fa-arrow-up" aria-hidden="true"></i>
                </span>
              </a>
            </header>
            <div class="card-content">
              <milton
                :sequence="sequence"
                :selectedIndex="selectedIndex"
                v-on:synthesis-update="synthesis = $event"
              ></milton>
              <div class="ref-footer">
                <reference
                  reflink="https://pubs.acs.org/doi/abs/10.1021/ja00172a020"
                  reftitle="Prediction of difficult sequences in solid-phase peptide synthesis"
                  journal="J. Am. Chem. Soc."
                  year="1990"
                  volume="112"
                  issue="16"
                  pages="6039-6046"
                  doi="10.1021/ja00172a020"
                ></reference>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section>
      <div class="container">
        <div class="columns">
          <div class="block column">
            <p>
              (c) <a href="https://twitter.com/andrewwhite01">Andrew White</a>, <a href="https://github.com/mehradans92">Mehrad Ansari</a>
              2022
            </p>
            <p>
              &nbsp;&nbsp;&nbsp;&nbsp;<a href="https://thewhitelab.org"
                >thewhitelab.org</a
              >
            </p>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import Version from "./Version.vue";
import SequenceViewer from "./SequenceViewer";
import Milton from "./results/Milton";
import SequenceInput from "./SequenceInput";
import TfPrediction from "./results/TfPrediction";
import Reference from "./Reference";
import pjson from "../../package.json";

export default {
  name: "App",
  components: {
    SequenceViewer,
    Milton,
    TfPrediction,
    SequenceInput,
    Reference,
    Version,
  },
  data() {
    return {
      sequence: "",
      viewWidth: 800,
      selectedIndex: -1,
      version: pjson["version"],
      nonfouling: null,
      hemolytic: null,
      soluble: null,
      synthesis: null,
      past: [],
    };
  },
  mounted: function () {
    this.viewWidth = this.$refs.sequencecontainer.offsetWidth;
  },
  computed: {
    screen() {
      return screen;
    },
    blob() {
      // get string of results table
      const tableText = this.past
        .map(
          (v) =>
            `${v.sequence},${v.hemolytic},${v.soluble},${v.nonfouling},${v.synthesis}`
        )
        .join("\n");
      const headerText = "Sequence,Hemolytic,Soluble,Nonfouling,Synthesis\n";
      // Create a blob of the data
      const blob = new Blob([headerText + tableText + "\n"], {
        type: "text/csv",
      });

      return window.URL.createObjectURL(blob);
    },
    resultsReady() {
      return (
        this.hemolytic && this.soluble && this.nonfouling && this.synthesis
      );
    },
  },
  methods: {
    pushSequence() {
      if (this.resultsReady) {
        this.past.push({
          sequence: this.sequence,
          hemolytic: this.hemolytic,
          soluble: this.soluble,
          nonfouling: this.nonfouling,
          synthesis: this.synthesis,
        });
      }
    },
  },
};
</script>

<style lang="scss">
.floating-hero {
  position: absolute;
  left: 0px;
  top: 0px;
  padding: 0.75rem;
  z-index: 2;
}

.column {
  margin: 1rem;
}
section {
  margin-bottom: 1.5em;
}

.tile {
  padding: 0.38rem;
}
.ref-footer {
  padding-right: 1.5rem;
  text-align: justify;
  @media screen and (min-width: 1024px) {
    position: absolute;
    margin-bottom: 1rem;
    bottom: 0rem;
  }
  @media screen and (max-width: 1023px) {
    margin-top: 1rem;
    margin-bottom: 1rem;
  }
}

.results-table {
  background-color: #f4f0e8;
  text-align: center;
}

.results-table th:last-child,
.results-table td:last-child {
  text-align: right;
}

.results-table th:first-child,
.results-table td:first-child {
  text-align: left;
  text-overflow: ellipsis;
  overflow: hidden;
  @media screen and (min-width: 1024px) {
    max-width: 20rem;
  }
  @media screen and (max-width: 1023px) {
    max-width: 10rem;
  }
}

.results-block {
  overflow-x: auto;
  max-height: 15rem;
  overflow-y: auto;
}
</style>
