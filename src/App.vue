<template>
  <div>
    <!-- <global-header project-name="Tools" /> -->
    <a-row class="list-compare-fields" :gutter="[60]">
      <div>
        <a-col :xs="{ span: 24 }" :lg="{ span: 12 }">
          <a-card :bordered="false">
            <div class="ant-card-meta-detail">
              <span class="ant-card-meta-title">List A</span>
              <div>
                <span class="dublicate-score">{{ dublicateScoreA }}</span>
                <span class="dublicate-score"> {{ lineScoreA }} </span>
              </div>
            </div>
            <div>
              <textarea
                rows="6"
                v-model="textAreaTextA"
                @input="textareaKeypresA()"
              ></textarea>
            </div>
            <template>
              <div class="card-footer-buttons">
                <div>
                  <a-button @click="moveTextareaListAToB">
                    <span>A → B</span>
                  </a-button>
                </div>
                <div class="d-flex">
                  <a-button @click="copyTextareaValuesA('success')">
                    <a-icon type="copy" />
                  </a-button>
                  <a-button @click="listSortingA()">
                    <a-icon type="sort-ascending" />
                  </a-button>
                  <a-button @click="openStatisticsModalA = true">
                    <a-icon type="bar-chart" />
                  </a-button>
                  <a-button @click="removedTextareListA">
                    <a-icon type="delete" />
                  </a-button>
                </div>
              </div>
            </template>
          </a-card>
        </a-col>
        <a-col :xs="{ span: 24 }" :lg="{ span: 12 }">
          <a-card :bordered="false">
            <div class="ant-card-meta-detail">
              <span class="ant-card-meta-title">List B</span>
              <div>
                <span class="dublicate-score">{{ dublicateScoreB }}</span>
                <span class="dublicate-score"> {{ lineScoreB }} </span>
              </div>
            </div>
            <div>
              <textarea
                rows="6"
                v-model="textAreaTextB"
                @input="textareaKeypresB()"
              ></textarea>
            </div>
            <template>
              <div class="card-footer-buttons">
                <div>
                  <a-button @click="moveTextareaListBToA">
                    <span>A ← B</span>
                  </a-button>
                </div>
                <div class="d-flex">
                  <a-button @click="copyTextareaValuesB('success')">
                    <a-icon type="copy" />
                  </a-button>
                  <a-button @click="listSortingB()">
                    <a-icon type="sort-ascending" />
                  </a-button>
                  <a-button @click="openStatisticsModalB = true">
                    <a-icon type="bar-chart" />
                  </a-button>
                  <a-button @click="removedTextareListB">
                    <a-icon type="delete" />
                  </a-button>
                </div>
              </div>
            </template>
          </a-card>
        </a-col>

        <div>
          <a-modal
            :footer="null"
            v-model="openStatisticsModalA"
            title="List Statistics"
          >
            <p>Words : {{ this.wordCountA }}</p>
            <p>Lines : {{ this.reallyLineScoreA }}</p>
            <p>Lines (non-empty) : {{ this.notEmptyLineScoreA.toString() }}</p>
            <button @click="openStatisticsModalA = false">OK</button>
          </a-modal>
        </div>
        <div>
          <a-modal
            :footer="null"
            v-model="openStatisticsModalB"
            title="List Statistics"
          >
            <p>Words : {{ this.wordCountB }}</p>
            <p>Lines : {{ this.reallyLineScoreB }}</p>
            <p>Lines (non-empty) : {{ this.notEmptyLineScoreB.toString() }}</p>
            <button @click="openStatisticsModalB = false">OK</button>
          </a-modal>
        </div>
      </div>
    </a-row>
    <a-row class="compare">
      <button class="button-compare" @click="compareLists">
        Compare Lists
      </button>
    </a-row>

    <a-row class="line-extra-tools">
      <a-col :xs="{ span: 12 }" :lg="{ span: 9, offset: 3 }">
        <a-col :xs="{ span: 22, offset: 1 }" :lg="{ span: 10 }">
          <a-checkbox @change="ignoreExtraSpacesChange($event)"
            >Ignore Extra Spaces</a-checkbox
          >
        </a-col>
        <a-col :xs="{ span: 22, offset: 1 }" :lg="{ span: 10 }">
          <a-checkbox @change="caseSensetiveChange($event)"
            >Case Sensitive</a-checkbox
          >
        </a-col>
        <a-col :xs="{ span: 22, offset: 1 }" :lg="{ span: 10 }">
          <a-checkbox @change="ignoreLeadingZeroesChange($event)"
            >Ignore Leading Zeroes</a-checkbox
          >
        </a-col>
        <a-col :xs="{ span: 22, offset: 1 }" :lg="{ span: 10 }">
          <a-checkbox @change="ignoreBeginAndSpacesChange($event)"
            >Ignore Begin End Spaces</a-checkbox
          >
        </a-col>
      </a-col>
      <a-col :xs="{ span: 12 }" :lg="{ span: 12 }">
        <a-select
          label-in-value
          :default-value="{ key: 'sortAz' }"
          style="width: 200px"
          @change="sortingChange($event)"
        >
          <a-select-option value="sortAz"> Sort A -> z </a-select-option>
          <a-select-option value="sortZa"> Sort z -> A </a-select-option>
        </a-select>
        <a-select
          label-in-value
          :default-value="{ key: 'noChange' }"
          style="width: 200px"
          @change="textChange($event)"
        >
          <a-select-option value="noChange"> No Change </a-select-option>
          <a-select-option value="capitalize"> Capitalize </a-select-option>
          <a-select-option value="uppercase"> Uppercase </a-select-option>
          <a-select-option value="lowercase"> Lowercase </a-select-option>
        </a-select>
      </a-col>
    </a-row>

    <a-row class="list-compare-results" :gutter="[20]">
      <ListCompareResults
        :compareResult="this.onlyAList"
        cardTitle="A Only"
        :lineScore="this.onlyAList.length"
        cardColor="danger"
      />
      <ListCompareResults
        :compareResult="this.aListIntersectionBList"
        cardTitle="A ∩ B"
        :lineScore="this.aListIntersectionBList.length"
        cardColor="warning"
      />
      <ListCompareResults
        :compareResult="this.onlyBList"
        cardTitle="B Only"
        :lineScore="this.onlyBList.length"
        cardColor="info"
      />
      <ListCompareResults
        :compareResult="this.aListCombinationBList"
        cardTitle="A ∪ B"
        :lineScore="this.aListCombinationBList.length"
        cardColor="success"
      />
    </a-row>
  </div>
</template>

<script>
import ListCompareResults from './components/listCompareResults.vue'
export default {
  components: {
    ListCompareResults
  },
  data: () => ({
    compareButtonDisable: true,
    onlyAList: [],
    onlyBList: [],
    arrayAList: [],
    arrayBList: [],
    textAreaListAtoB: null,
    textAreaListBtoA: null,
    aListCombinationBList: [],
    aListIntersectionBList: [],
    dublicateScoreA: 0,
    dublicateScoreB: 0,
    lineScoreA: 0,
    lineScoreB: 0,
    textAreaTextA: null,
    textAreaTextB: null,
    openStatisticsModalA: false,
    openStatisticsModalB: false,
    reallyLineScoreA: "",
    notEmptyLineScoreA: "",
    reallyLineScoreB: "",
    notEmptyLineScoreB: "",
    wordCountA: "",
    wordCountB: "",
    notNullArrayA: [],
    notNullArrayB: [],
    caseSensetiveChecked: false,
    ignoreBeginAndSpacesChecked: false,
    ignoreLeadingZeroesChecked: false,
    ignoreExtraSpacesChecked: false,
    sortingKey: "",
    textKey: "",
  }),
  methods: {
    textChange(event) {
      this.textKey = event.key;
    },
    sortingChange(event) {
      this.sortingKey = event.key;
    },
    caseSensetiveChange(event) {
      this.caseSensetiveChecked = event.target.checked;
    },
    ignoreBeginAndSpacesChange(event) {
      this.ignoreBeginAndSpacesChecked = event.target.checked;
    },
    ignoreLeadingZeroesChange(event) {
      this.ignoreLeadingZeroesChecked = event.target.checked;
    },
    ignoreExtraSpacesChange(event) {
      this.ignoreExtraSpacesChecked = event.target.checked;
    },
    removedTextareListA() {
      this.textAreaTextA = "";
      this.lineScoreA = 0;
      this.dublicateScoreA = 0;
    },
    removedTextareListB() {
      this.textAreaTextB = "";
      this.lineScoreB = 0;
      this.dublicateScoreB = 0;
    },
    copyTextareaValuesA(type) {
      const copyText = this.textAreaTextA;
      const textArea = document.createElement("textarea");
      textArea.textContent = copyText;
      textArea.classList.add("textarea-input");
      document.body.append(textArea);
      textArea.select();
      document.execCommand("copy");
      this.$notification[type]({
        message: "Copy successful",
      });
    },
    copyTextareaValuesB(type) {
      const copyText = this.textAreaTextB;
      const textArea = document.createElement("textarea");
      textArea.textContent = copyText;
      textArea.classList.add("textarea-input");
      document.body.append(textArea);
      textArea.select();
      document.execCommand("copy");
      this.$notification[type]({
        message: "Copy successful",
      });
    },
    listSortingA() {
      this.textAreaTextA = this.notNullArrayA.sort().join("\n").split(",");
    },
    listSortingB() {
      this.textAreaTextB = this.notNullArrayB.sort().join("\n").split(",");
    },
    textareaKeypresA() {
      if (this.textAreaTextA != null) {
        let textareaValue = this.textAreaTextA;
        this.wordCountA = textareaValue.split(/[\s]+/).length;
        textareaValue = textareaValue.split("\n");
        this.reallyLineScoreA = textareaValue.length;
        this.notNullArrayA = [];
        let similarLineCounter = 0;
        textareaValue.map((e) => {
          if (e != "") {
            this.notNullArrayA.push(e);
            this.lineScoreA = this.notNullArrayA.length;
          }
          this.notEmptyLineScoreA = this.notNullArrayA.length;
        });
        const counts = {};
        this.notNullArrayA.forEach(function (x) {
          counts[x] = (counts[x] || 0) + 1;
        });
        Object.values(counts).map((x) => {
          if (x > 1) {
            similarLineCounter++;
          }
        });
        this.dublicateScoreA = similarLineCounter;
        if (this.textAreaTextA.length == 0) {
          this.lineScoreA = "0";
          this.dublicateScoreA = "0";
        }
      }
    },
    textareaKeypresB() {
      if (this.textAreaTextB != null) {
        let textareaValue = this.textAreaTextB;
        this.wordCountB = textareaValue.split(/[\s]+/).length;
        textareaValue = textareaValue.split("\n");
        this.reallyLineScoreB = textareaValue.length;
        this.notNullArrayB = [];
        let similarLineCounter = 0;
        textareaValue.map((e) => {
          if (e != "") {
            this.notNullArrayB.push(e);
            this.lineScoreB = this.notNullArrayB.length;
          }
          this.notEmptyLineScoreB = this.notNullArrayB.length;
        });
        const counts = {};
        this.notNullArrayB.forEach(function (x) {
          counts[x] = (counts[x] || 0) + 1;
        });
        Object.values(counts).map((x) => {
          if (x > 1) {
            similarLineCounter++;
          }
        });
        this.dublicateScoreB = similarLineCounter;
        if (this.textAreaTextB.length == 0) {
          this.lineScoreB = "0";
          this.dublicateScoreB = "0";
        }
      }
    },
    moveTextareaListAToB() {
      this.textAreaTextB = this.textAreaTextA;
      this.textAreaTextA = null;
      this.textareaKeypresB();
      this.lineScoreA = 0;
      this.dublicateScoreA = 0;
    },
    moveTextareaListBToA() {
      this.textAreaTextA = this.textAreaTextB;
      this.textAreaTextB = null;
      this.lineScoreB = 0;
      this.dublicateScoreB = 0;
      this.textareaKeypresA();
    },
    lineFiltering(e) {
      if (this.caseSensetiveChecked == false) {
        e = e.toLowerCase();
      }
      if (this.ignoreBeginAndSpacesChecked == true) {
        e = e.trim();
      }
      if (this.ignoreExtraSpacesChecked == true) {
        e = e.replace(/ +(?= )/g, "").trim();
      }
      if (this.ignoreLeadingZeroesChecked == true) {
        e = e.replace(/^0+/, "");
      }
      if (this.textKey == "capitalize") {
        e = e.charAt(0).toUpperCase() + e.slice(1).toLowerCase();
      }
      if (this.textKey == "uppercase") {
        e = e.toUpperCase();
      }
      if (this.textKey == "lowercase") {
        e = e.toLowerCase();
      }
      this.onlyAList.sort();
      this.onlyBList.sort();
      this.aListCombinationBList.sort();
      this.aListIntersectionBList.sort();
      if (this.sortingKey == "sortZa") {
        this.onlyAList.reverse();
        this.onlyBList.reverse();
        this.aListCombinationBList.reverse();
        this.aListIntersectionBList.reverse();
      }
      if (e != "" || e != undefined) {
        return e.toString();
      }
    },
    compareLists() {
      if (this.textAreaTextA != null && this.textAreaTextB != null) {
        const listA = [];
        const listB = [];
        this.onlyAList = [];
        this.onlyBList = [];
        this.aListCombinationBList = [];
        this.aListIntersectionBList = [];
        listA.push(...this.textAreaTextA.toString().split("\n"));
        listB.push(...this.textAreaTextB.toString().split("\n"));
        listA.map((e) => {
          if (
            !listB.includes(e) &&
            !this.onlyAList.includes(this.lineFiltering(e))
          ) {
            this.onlyAList.push(this.lineFiltering(e));
          }
          if (
            listB.includes(e) &&
            !this.aListIntersectionBList.includes(this.lineFiltering(e))
          ) {
            this.aListIntersectionBList.push(this.lineFiltering(e));
          }
          if (!this.aListCombinationBList.includes(this.lineFiltering(e))) {
            this.aListCombinationBList.push(this.lineFiltering(e));
          }
        });
        listB.map((e) => {
          if (
            !listA.includes(e) &&
            !this.onlyBList.includes(this.lineFiltering(e))
          ) {
            this.onlyBList.push(this.lineFiltering(e));
          }
          if (!this.aListCombinationBList.includes(this.lineFiltering(e))) {
            this.aListCombinationBList.push(this.lineFiltering(e));
          }
        });
        this.onlyAList.map((e, index) => {
          if (e == "" || e == undefined) {
            this.onlyAList.splice(index, 1);
          }
        });
        this.onlyBList.map((e, index) => {
          if (e == "" || e == undefined) {
            this.onlyBList.splice(index, 1);
          }
        });
        this.aListCombinationBList.map((e, index) => {
          if (e == "" || e == undefined) {
            this.aListCombinationBList.splice(index, 1);
          }
        });
        this.aListIntersectionBList.map((e, index) => {
          if (e == "" || e == undefined) {
            this.aListIntersectionBList.splice(index, 1);
          }
        });
      } else {
        this.$notification["error"]({
          message: "fields are empty",
        });
      }
    },
  },
};
</script>
<style lang="scss">
@import "./assets/scss/listCompare.scss";
</style>
