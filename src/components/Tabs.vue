<template>
  <div>
    <v-tabs
      grow
      color="lightBackground"
    >
      <v-tab :key="1" ripple>
        Stylize an image
      </v-tab>
      <v-tab :key="2" ripple>
        Combine two styles
      </v-tab>

      <v-tabs-items v-model="active"
        :touchless="true"
      >
        <transition
          name="fade"
          mode="out-in"
        >
          <v-tab-item :key="1">
            <v-card flat>
              <StylizePanelLayout
                ref="stylePanel1"
              />
            </v-card>
          </v-tab-item>
        </transition>

        <transition
          name="fade"
          mode="out-in"
        >
          <v-tab-item :key="2">
            <v-card flat>
              <StylizePanelLayout
                ref="stylePanel2"
                twoStyles
              />
            </v-card>
          </v-tab-item>
        </transition>

      </v-tabs-items>
    </v-tabs>
  </div>
</template>

<script>
  import StylizePanelLayout from './StylizePanelLayout'

  export default {
    data() {
      return {
        active: null
      }
    },
    components: {
      StylizePanelLayout
    }
  }
</script>
