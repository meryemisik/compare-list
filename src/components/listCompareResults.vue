<template>
 <div>
  <a-col :xs="{ span: 12 }" :lg="{ span: 6, offset: 0 }" :class="[cardColor]">
    <a-card :bordered="false">
      <div class="ant-card-meta-detail">
        <span class="ant-card-meta-title">{{ cardTitle }}</span>
        <div>
          <span class="dublicate-score"> {{ lineScore }} </span>
        </div>
      </div>
      <div>
        <textarea id="myTextArea" rows="6" v-model="seperatedResult" @input="textareaKeypress()"></textarea>
      </div>
      <template>
        <div class="card-footer-buttons">
          <div class="d-flex">
            <a-button    @click="copyTextareaValues('success')">
              <a-icon type="copy" />
            </a-button>
            <a-button    @click="openStatisticsModal = true">
              <a-icon type="bar-chart" />
            </a-button>
          </div>
        </div>
      </template>
    </a-card>
  </a-col>
  <div>
      <a-modal
        :footer="null"
        v-model="openStatisticsModal"
        title="List Statistics"
      >
        <p>Words : {{ this.wordCount }}</p>
        <p>Lines : {{ this.reallyLineScore }}</p>
        <p>Lines (non-empty) : {{ this.notEmptyLineScore.toString() }}</p>
        <button @click="openStatisticsModal = false">OK</button>
      </a-modal>
    </div>
 </div>
</template>
<script>
export default {
  props: {
    cardTitle: {
      type: String,
      default: "",
    },
    dublicateScore: {
      type: Number,
      default: 0,
    },
    lineScore: {
      type: Number,
      default: 0,
    },
    cardColor: {
      type: String,
      default: "info",
    },
    compareResult: {
      type: Array,
    },
  },
  data() {
    return {
    seperatedResult:'',
    openStatisticsModal: false,
    reallyLineScore: "",
    notEmptyLineScore: 0,
    wordCount: "",
    notNullArray: [],
    }
  },
  methods: {
    seperateData(arr) {
     return this.seperatedResult= arr.join("\n").split(",");
    },
    textareaKeypress() {
      let textareaValue = this.seperatedResult;
      this.wordCount = textareaValue.split(/[\s]+/).length;
      textareaValue = textareaValue.split("\n");
      this.reallyLineScore = textareaValue.length;

      this.notNullArray=[]; 
      textareaValue.map((e) => {
        if (e != "") {
          this.notNullArray.push(e);
          this.lineScore = this.notNullArray.length;
        } 
       this.notEmptyLineScore = this.notNullArray.length;
      });
      if (this.seperatedResult.length == 0) {
        this.lineScore = "0";
        this.dublicateScore = "0";
      }
    },
    copyTextareaValues(type) {
      const copyText = this.seperatedResult;
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
  },
  watch:{
    compareResult(){
      this.seperateData(this.compareResult);
      this.reallyLineScore = this.lineScore
   }
   
  },
};
</script>
<style lang="scss">
@import "../assets/scss/listCompareResults.scss";
</style>
