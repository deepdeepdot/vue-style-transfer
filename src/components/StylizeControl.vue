<template>
  <div>
    <v-container fluid>

      <v-layout row>
        <v-slider
          thumb-label
          ref="slider"
          v-model="slider"
          :label="sliderLabel"
        ></v-slider>
      </v-layout>

      <v-layout row>
        <v-flex grow>
          <v-card dark>
            <v-btn
              ref="styleButton"
              color="pink"
              block
              @click="styleAction"
            >{{ buttonLabelValue }}
            </v-btn>
          </v-card>
        </v-flex>
        <!-- <v-flex shrink>
          <v-card dark>
            <v-btn
              color="pink"
              block
              @click="randomize"
            >Randomize</v-btn>
          </v-card>
        </v-flex> -->
      </v-layout>

      <v-layout row>
        <v-flex>
          <v-select
            ref="modelSelectStyle"
            :value="style"
            :items="styleOptions"
            @change="loadStyle($event)"
          ></v-select>
        </v-flex>
      </v-layout>

      <v-layout row>
        <v-flex>
          <v-select
            ref="modelSelectTransformer"
            :value="transform"
            :items="transformOptions"
            @change="loadTransform($event)"
          ></v-select>
        </v-flex>
      </v-layout>

    </v-container>
  </div>
</template>

<script>

const styleOptions = [
    "[Fast] (9.6MB) Distilled MobileNet style model",
    "[High quality] (36.3MB) Original Inceptionv3 style model"
  ],
  transformOptions = [
    "[Fast] (2.4MB) Separable_conv2d transformer",
    "[High quality] (7.9MB) Original transformer model"
  ];

export default {
  name: "StylizeControl",
  props: {
    buttonLabel: String,
    sliderLabel: String,
  },
  data() {
    return {
      stylizeButtonLabel: null,
      slider: 70,
      styleOptions,
      transformOptions,
    };
  },
  computed: {
    buttonLabelValue() {
      return this.stylizeButtonLabel || this.buttonLabel;
    },
    style() {
      return this.$store.state.style;
    },
    transform() {
      return this.$store.state.transform;
    },
    styleTransfer() {
      return this.$store.state.styleTransfer;
    }
  },
  mounted() {
    this.disableStylizeButtons();
    this.$store.commit('styleUpdate', styleOptions[0]);
    this.$store.commit('transformUpdate', transformOptions[0]);
  },
  methods: {
    reportError(err) {
      alert(err);
    },
    reportStatus(msg) {
      this.stylizeButtonLabel = msg;
    },
    styleAction() {
      this.$emit('styleAction');
    },
    // randomize() {
    //   alert('not implemented');
    // },
    enableStylizeButtons() {
      this.$refs['styleButton'].disable = false;
      this.$refs['modelSelectStyle'].disable = false;
      this.$refs['modelSelectTransformer'].disable = false;
    },
    disableStylizeButtons() {
      this.$refs['styleButton'].disable = false;
      this.$refs['modelSelectStyle'].disable = false;
      this.$refs['modelSelectTransformer'].disable = false;
    },
    async initializeModels() {
      let { loadMobileNetStyleModel, loadSeparableTransformerModel } = this.styleTransfer,
          { reportStatus, reportError } = this;

      try {
        this.disableStylizeButtons();
        await Promise.all([
          loadMobileNetStyleModel(reportStatus, reportError),
          loadSeparableTransformerModel(reportStatus, reportError),
        ]);
        this.$emit('modelLoaded');
      } catch(error) {
        reportError(error);
      }
    },
    async loadStyle(event) {
      let type = event.startsWith('[Fast]') ? 'MOBILE_STYLE_NET': 'INCEPTION_STYLE_NET',
          { loadMobileNetStyleModel, loadInceptionStyleModel } = this.styleTransfer,
          { reportStatus, reportError } = this;

      try {
        this.disableStylizeButtons();
        if (type === 'MOBILE_STYLE_NET') {
          await loadMobileNetStyleModel(reportStatus, reportError);
        } else if (type === 'INCEPTION_STYLE_NET') {
          await loadInceptionStyleModel(reportStatus, reportError);
        }
        this.$emit('modelLoaded');
        this.$store.commit('styleUpdate', event);
      } catch(error) {
        reportError(error);
      }
    },
    async loadTransform(event) {
      let type = event.startsWith('[Fast]') ? 'SEPARABLE_TRANSFORM_NET' : 'ORIGINAL_TRANSFORM_NET',
          { loadSeparableTransformerModel, loadOriginalTransformerModel } = this.styleTransfer,
          { reportStatus, reportError } = this;

      try {
        this.disableStylizeButtons();
        if (type === 'SEPARABLE_TRANSFORM_NET') {
          await loadSeparableTransformerModel(reportStatus, reportError);
        } else if (type === 'ORIGINAL_TRANSFORM_NET') {
          await loadOriginalTransformerModel(reportStatus, reportError);
        }
        this.$emit('modelLoaded');
        this.$store.commit('transformUpdate', event);
      } catch(error) {
        reportError(error);
      }
    }
  }
};
</script>
