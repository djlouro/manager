<template>
  <div>
    <CRow>
      <CCol col="6">
          <CCard>
            <CCardHeader>
                <CIcon name="cil-lock-locked"/>
                Mooring information
            </CCardHeader>
            <CCardBody>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Code
                    </CCol>
                    <CCol md="9">
                        <CInput v-model="mooring.code" placeholder="Code">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Dimensions
                    </CCol>
                    <CCol md="4">
                        <CInput v-model="mooring.length" placeholder="Length"></CInput>
                    </CCol>
                    <CCol md="5">
                        <CInput v-model="mooring.width" placeholder="Width">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Wather depth
                    </CCol>
                    <CCol md="9">
                        <CInput v-model="mooring.watherDepth" placeholder="Wather depth">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Type
                    </CCol>
                    <CCol md="9">
                        <CSelect
                          v-model="mooring.type"
                          :options="['Wather','Land']"
                        >
                        </CSelect>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Price per month
                    </CCol>
                    <CCol md="9">
                        <CInput v-model="mooring.price" placeholder="Price per month">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Condition
                    </CCol>
                    <CCol md="9">
                        <CSelect
                          v-model="mooring.condition"
                          :options="['avalible','taken','repair']"
                        >
                        </CSelect>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Position
                    </CCol>
                    <CCol md="4">
                        <CInput v-model="mooring.position.lat" placeholder="Latitude"></CInput>
                    </CCol>
                    <CCol md="5">
                        <CInput v-model="mooring.position.lng" placeholder="Langitude">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Additional notes
                    </CCol>
                    <CCol md="9">
                        <CInput v-model="mooring.notes" placeholder="Notes">></CInput>
                    </CCol>
                </CRow>
            </CCardBody>
          </CCard>
      </CCol>
      <CCol col="6">
          <CCard>
            <CCardHeader>
                <CIcon name="cil-lock-locked"/>
                Subscriber information
            </CCardHeader>
            <CCardBody>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Username
                    </CCol>
                    <CCol md="9">
                        <CInput v-model="mooring.username" placeholder="Usernme">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Full name
                    </CCol>
                    <CCol md="9">
                        <CInput placeholder="Name and surname">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Street
                    </CCol>
                    <CCol md="9">
                        <CInput placeholder="Street">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Address
                    </CCol>
                    <CCol md="4">
                        <CInput placeholder="ZIP"></CInput>
                    </CCol>
                    <CCol md="5">
                        <CInput placeholder="Town">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Country
                    </CCol>
                    <CCol md="9">
                        <CInput placeholder="Country">></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Boat type
                    </CCol>
                    <CCol md="9">
                        <CInput placeholder="Boat type"></CInput>
                    </CCol>
                </CRow>
                <CRow align-vertical="center" class="mb-2">
                    <CCol md="3">
                        Boat dimensions
                    </CCol>
                    <CCol md="4">
                        <CInput placeholder="Length"></CInput>
                    </CCol>
                    <CCol md="5">
                        <CInput placeholder="Width">></CInput>
                    </CCol>
                </CRow>
            </CCardBody>
          </CCard>
      </CCol>
    </CRow>
    <CRow>
      <CCol align="right" cols="6" class="pr-0">
          <CButton color="link" to="/moorings">Cancel</CButton>
      </CCol>
      <CCol align="left" cols="6" class="pl-0">
          <CButton color="primary" @click="saveMooring">Save</CButton>
      </CCol>
    </CRow>
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
export default {
  name: 'Moorings',
  data () {
    return {
      editUser: false,
      mooring: {
            id: "",
            code: "",
            width: "",  
            length: "",
            watherDepth: "",
            type: "",
            price: "",
            condition: "",
            client: "",
            position: {
                lat: null,
                lng: null
            },
          },
    }
  },
  computed: {
  },
  methods: {
    saveMooring() {
      console.log(this.mooring)
      console.log(this.editUser)
      this.mooring.position.lat = parseFloat(this.mooring.position.lat)
      this.mooring.position.lng = parseFloat(this.mooring.position.lng)
      if (this.editUser) {
        axios({
        method: "PUT",
        url: "http://localhost:3000/moorings/"+this.mooring.id,
        data: this.mooring
        }).then(r => {
          console.log("aaa", r)
          this.$router.push({name: 'Moorings'})
        }) 
      } 
      else {
        axios({
          method: "POST",
          url: "http://localhost:3000/moorings",
          data: this.mooring
        }).then(r => {
          console.log("aaa", r)
          this.$router.push({name: 'Moorings'})
        })
      }
    }
    
  },
  created() {
    if (this.$route.name === 'MooringsEdit') {
      this.mooring = this.$store.state.selectedMooring
      this.editUser = true
    }
  }
}
</script>
