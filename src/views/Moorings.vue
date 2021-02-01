<template>
  <div>
    <CRow>
      <CCol>
          <CCard>
            <CCardHeader>
                <CRow>
                    <CCol align="left">
                        <CIcon name="cil-options"/>
                        Mooring List
                    </CCol>
                    <CCol align="right">
                        <CButton color="primary" size="sm" to="/moorings/create">
                          <CIcon name="cil-plus"/>
                          Add mooring
                          </CButton>
                    </CCol>
                </CRow>
            </CCardHeader>
            <CCardBody>
            <CDataTable
                sortable
                :items="mooringList"
                :fields="fields"
                :sorter="true"
                :column-filter="true">
              <template #condition="{item}">
                <td>
                  <CBadge v-if="item.condition === 'avalible'" color="success">{{item.condition.toUpperCase()}}</CBadge>
                  <CBadge v-else-if="item.condition === 'taken'" color="danger">{{item.condition.toUpperCase()}}</CBadge>
                  <CBadge v-else color="warning">{{item.condition.toUpperCase()}}</CBadge>
                </td>
              </template>
              <template #price="{item}">
                <td>
                  <div v-if="item.price">
                    {{item.price}}€
                  </div>
                </td>
              </template>
              <template #edit="{item}">
                        <td>
                            <CButton variant="ghost"
                                     color="primary"
                                     size="sm"
                                     class="py-0 px-1 mr-3"
                                     @click="editMooring(item)">
                                <CIcon name="cil-pencil"/>
                            </CButton>
                            <CButton variant="ghost"
                                     color="primary"
                                     size="sm"
                                     class="py-0 px-1 mr-3"
                                     @click="deleteMooring(item)">
                                <CIcon name="cil-trash"/>
                            </CButton>
                        </td>
                    </template>
            </CDataTable>
            </CCardBody>
          </CCard>
      </CCol>
    </CRow>
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios';

export default {
  name: 'Moorings',
  data () {
    return {
      mooringList: [
      ]
    }
  },
  computed: {
        fields() {
            return [
                {key: 'code', label: "Code", sortable: true},
                {key: 'length', label: "Length (up to)",sortable: true},
                {key: 'width', label: "Width (up to)",sortable: true},
                {key: 'condition', label: "Status", sortable: true},
                {key: 'price', label: "Price / month", sortable: true},
                {key: 'edit', label: "Edit / Delete", sortable: false, filter: false}
            ]
    },
  },
  methods: {
    editMooring(item) {
        this.$store.state.selectedMooring = item
        this.$router.push({name: 'MooringsEdit'})
    },
    deleteMooring(item) {
      axios({
          method: 'DELETE',
          url: 'http://localhost:3000/moorings/'+item.id
    }).then(r => {
      this.getMoorings()
    })
    },
    getMoorings() {
      axios({
          method: 'GET',
          url: 'http://localhost:3000/moorings'
    }).then(r => {
        this.mooringList = r.data
      })
    }
  },
  created() {
    this.getMoorings()
  }
}
</script>
