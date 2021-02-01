<template>
  <div>
    <div v-if="$store.state.userGroup === 'ADMIN'">
    <CRow>
      <CCol sm="6" lg="3">
        <CWidgetProgress
          header="All moorings"
          :text="(moorings.all).toString()"
          color="info"
          inverse
          :value="100"
        />
      </CCol>
      <CCol sm="6" lg="3">
        <CWidgetProgress
          header="Avalible moorings"
          :text="(moorings.avalible).toString()"
          color="success"
          inverse :value="moorings.avalible / moorings.all * 100"
        />
      </CCol>
      <CCol sm="6" lg="3">
        <CWidgetProgress
          header="Taken moorings"
           :text="(moorings.taken).toString()"
          color="danger"
          inverse :value="moorings.taken / moorings.all * 100 "
        />
      </CCol>
      <CCol sm="6" lg="3">
        <CWidgetProgress
          header="Repair required"
           :text="(moorings.repair).toString()"
          color="warning"
          inverse :value="moorings.repair / moorings.all * 100 "
        />
        
      </CCol>
    </CRow>
    <CRow>
      <CCol col="6">
          <CCard>
            <CCardHeader>
              Mooring map
            </CCardHeader>
            <CCardBody>
                <GmapMap
              :center="center"
              :zoom="19"
              style="height: 600px"
            >
              <GmapMarker
                :key="index"
                v-for="(m, index) in mooringList"
                :position="m.position"
                :title="m.code"
                x
                :clickable="false"
                :draggable="m.draggable"
                :icon="{ url: 'https://icons.iconarchive.com/icons/iconsmind/outline/24/Ship-2-icon.png'}"
              />
            </GmapMap>
            </CCardBody>
          </CCard>
      </CCol>
      <CCol COL=6>
          <CCard>
            <CCardHeader>Mooring List</CCardHeader>
            <CCardBody>
            <CDataTable
                :items="repairMoorings"
                :fields="fields">
              <template #status="{item}">
                <td>
                  <CBadge v-if="item.condition === 'repair'" color="warning">REPAIR</CBadge>
                </td>
              </template>
              <template #price="{item}">
                <td>
                  {{item.price}}€
                </td>
              </template>
            </CDataTable>
            </CCardBody>
          </CCard>
      </CCol>
    </CRow>
    </div>
    <div v-else>
      <h1>Your mooring information</h1>
      <CDropdownDivider/>
        <div v-if="userMooring[0]">
          <h2>Code</h2>
        <h4 class="text-muted">{{userMooring[0].code}}</h4>
        <CDropdownDivider/>
        <h2>Width & Length</h2>
        <h4 class="text-muted">{{userMooring[0].width}}
        {{userMooring[0].length}}</h4>
        <CDropdownDivider/>
        <h2>Wather depth</h2>
        <h4 class="text-muted">{{userMooring[0].watherDepth}}</h4>
        <CDropdownDivider/>
        <h2>Price</h2>
        <h4 class="text-muted">{{userMooring[0].price}}</h4>
        <CDropdownDivider/>
        <h4 class="text-muted">
        <CBadge v-if="userMooring[0].condition === 'avalible'" color="success">{{userMooring[0].condition.toUpperCase()}}</CBadge>
                  <CBadge v-else-if="userMooring[0].condition === 'taken'" color="danger">{{userMooring[0].condition.toUpperCase()}}</CBadge>
                  <CBadge v-else color="warning">{{userMooring[0].condition.toUpperCase()}}</CBadge></h4>
        </div>
        <div v-else>
          <CAlert color="danger" closeButton>
            You don't own any mooring at the moment!!!
          </CAlert>
        </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import * as VueGoogleMaps from 'vue2-google-maps'
import TheSidebarVue from '../containers/TheSidebar.vue'
 
Vue.use(VueGoogleMaps, {
  load: {
    key: 'AIzaSyB1sB2py3ww1fAiwpi7xyEtmYlovmPZiEk',
  }})

export default {
  name: 'Dashboard',
  components: {
    VueGoogleMaps
  },
  data () {
    return {
      center: {lat: 45.547511, lng: 13.724488},
      mooringList: [],
      moorings: {
        all: 0,
        avalible: 0,
        taken: 0,
        repair: 0
      },
      repairMoorings: [],
      userMooring: [],
      group: null
    }
  },
  computed: {
        fields() {
            return [
                {key: 'code', label: "Code", sortable: false},
                {key: 'length', label: "Length (up to)",sortable: true},
                {key: 'width', label: "Width (up to)",sortable: true},
                {key: 'status', label: "Status", sortable: false},
                {key: 'price', label: "Price / month", sortable: false}
            ]
    },
  },
  methods: {
    setData() {
      if (this.group !== 'ADMIN') {
        this.getMooringsInfo()
      }
      for (let i = 0; i < this.mooringList.length; i++) {
        if (this.mooringList[i].condition === 'taken') {
          this.moorings.taken ++
        }
        if (this.mooringList[i].condition === 'avalible') {
          this.moorings.avalible ++
        }
        if (this.mooringList[i].condition === 'repair') {
          this.moorings.repair ++
        }
        this.moorings.all++
      }
    },
    getMooringsInfo() {
      axios({
          method: 'GET',
          url: 'http://localhost:3000/moorings?client='+this.$store.state.username
    }).then(r => {
        this.userMooring = r.data
      })
    },
    getMoorings() {
      axios({
          method: 'GET',
          url: 'http://localhost:3000/moorings'
    }).then(r => {
        this.mooringList = r.data
        this.setData()
      })
    },
    getRepairMoorings() {
      axios({
          method: 'GET',
          url: 'http://localhost:3000/moorings?condition=repair'
    }).then(r => {
        this.repairMoorings = r.data
      })
    }
  },
  created() {
    this.getMoorings()
    this.getRepairMoorings()
  }
}
</script>

<style>
.gm-style-mtc {
  display: none;
}
</style>
