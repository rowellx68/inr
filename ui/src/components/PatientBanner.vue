<template>
  <v-card class="mb-4">
    <v-layout row align-baseline class="pa-2">
       <v-flex xs4>
       </v-flex>
       <v-flex xs4 class="headline text-xs-center">
         INR Module
       </v-flex>
       <v-flex xs4 v-if="patientContext">
         <v-layout row justify-space-between>
           <b>{{patientContext.gp.name}}</b>
           <span>{{now}}</span>
         </v-layout>
       </v-flex>
    </v-layout>
    <transition name="reveal">
      <div v-if="patient" key="patient">
        <v-layout row class="purple darken-1 white--text pl-2 pr-2 pt-3 pb-3 justify-space-between">
          <span class="body-1">{{patientName}}</span>
          <div>
            <span class="caption">Born:</span>
            <span class="body-1">{{patient.dateOfBirth | date}} ({{patientAge}}y)</span>
          </div>
          <div>
            <span class="caption">Gender:</span>
            <span class="body-1">{{patient.gender}}</span>
          </div>
          <div>
            <span class="caption">NHS Number:</span>
            <span class="body-1">{{patient.nhsNumber}}</span>
          </div>
          <div>
            <span class="caption">GP Practice:</span>
            <span class="body-1">{{patientContext.gp.address.line1}}</span>
          </div>
        </v-layout>
        <v-layout row class="pa-2" justify-space-between>
          <div class="pr-1">
            <span class="caption">Address:</span>
            <span class="body-1 address">{{address}}</span>
          </div>
          <template v-if="treatmentPlan">
            <div class="px-1">
              <span class="caption">Diagnosis:</span>
              <span class="body-1">{{treatmentPlan.diagnosis}}</span>
            </div>
            <div class="px-1">
              <span class="caption">Drug:</span>
              <span class="body-1">{{treatmentPlan.drug}}</span>
            </div>
            <div class="px-1">
              <span class="caption">Target INR:</span>
              <span class="body-1">{{treatmentPlan.targetINR}}</span>
            </div>
            <div class="px-1">
              <span class="caption">Date:</span>
              <span class="body-1">{{treatmentPlan.treatmentDuration}}</span>
            </div>
          </template>
        </v-layout>
      </div>
    </transition>
  </v-card>
</template>

<script>
import moment from 'moment-es6'
import { mapState } from 'vuex'

export default {
  name: 'patient-banner',
  data () {
    return {
      now: moment().format('DD-MMM-YYYY')
    }
  },
  computed: {
    ...mapState({
      patient: state => state.patient,
      patientContext: state => state.patientContext,
      treatmentPlan: state => state.treatmentPlan
    }),
    patientAge () {
      let dob = moment(this.patient.dateOfBirth)
      return moment().diff(dob, 'years')
    },
    patientName () {
      let lastName = this.patient.lastName.toUpperCase()
      return `${lastName}, ${this.patient.firstName}. (${this.patient.title})`
    },
    address () {
      let addrArray = []
      let fields = ['line1', 'line2', 'line3', 'line4', 'postcode']
      fields.forEach(field => {
        if (this.patient.address[field]) {
          addrArray.push(this.patient.address[field])
        }
      })
      return addrArray.join(', ')
    }
  },
  filters: {
    date (value) {
      return moment(value).format('DD-MMM-YYYY')
    }
  }
}
</script>

<style scoped>
.reveal-enter-active, .reveal-leave-active {
  transition: opacity 0.3s, max-height 0.3s;
  overflow: hidden;
  max-height: 100px;
}
.reveal-enter, .reveal-leave-to {
  opacity: 0;
  max-height: 0px;
}
</style>
