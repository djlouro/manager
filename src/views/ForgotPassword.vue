<template>
  <div class="c-app flex-row align-items-center bg-img">
      <CContainer>
            <CRow class="justify-content-center">
                <CCol lg="5" md="6" sm="8">
                    <CCardGroup>
                        <CCard class="p-4 shadow-lg">
                            <CCardBody>
                                <h1>Reset password</h1>
                                <p v-if="!showAll" class="text-muted">Enter your username and click get code. You will get an email with confirmation code.</p>
                                <CForm>
                                     <CInput v-model="username"
                                            :disabled="showAll"
                                            placeholder="Username"
                                            @input="onInput()">
                                        <template #prepend-content>
                                            <CIcon name="cil-user"/>
                                        </template>
                                    </CInput>
                                    <p v-if="showAll" class="text-muted">Set new password below</p>
                                    <CInput v-model="code"
                                            v-if="showAll"
                                            placeholder="Code"
                                            @input="onInput()">
                                        <template #prepend-content>
                                            <CIcon name="cil-calculator"/>
                                        </template>
                                    </CInput>
                                    <CInput v-model="newPassword"
                                            v-if="showAll"
                                            type="password"
                                            placeholder="Password"
                                            @input="onInput()">
                                        <template #prepend-content>
                                            <CIcon name="cil-lock-locked"/>
                                        </template>
                                    </CInput>
                                    <CInput v-model="newPassword2"
                                            v-if="showAll"
                                            type="password"
                                            placeholder="Password"
                                            @input="onInput()">
                                        <template #prepend-content>
                                            <CIcon name="cil-lock-locked"/>
                                        </template>
                                    </CInput>
                                    <span class="text-danger">
                                        {{errorMsg}}
                                    </span>
                                    <CRow>
                                        <CCol v-if="!showAll" class="text-left">
                                            <CButton color="primary" class="px-4" @click="getCode()">Get code</CButton>
                                        </CCol>
                                        <CCol v-if="showAll" class="text-left">
                                            <CButton color="primary" class="px-4" @click="resetPassword()">Save new passowrd</CButton>
                                        </CCol>
                                        <CCol class="text-right">
                                            <CButton color="link" class="px-0" to="/login">Back to login</CButton>
                                        </CCol>
                                    </CRow>

                                </CForm>
                            </CCardBody>
                        </CCard>
                    </CCardGroup>
                </CCol>
            </CRow>
      </CContainer>
  </div>
</template>

<script>
import Vue from 'vue'
import {Auth} from "aws-amplify";

export default {
  name: 'ForgotPassword',
  data () {
    return {
        showAll: false,
        completeNewPassword: false,
        errorMsg: '',
        user: null,
        username: '',
        code: '',
        password: '',
        newPassword: '',
        newPassword2: '',
    }
  },
  computed: {
    
  },
  methods: {
      getCode() {
          Auth.forgotPassword(this.username)
          .then(() => {
              this.showAll = true
          })
      },
      resetPassword() {
          Auth.forgotPasswordSubmit(this.username, this.code, this.newPassword)
          .then(() => {
              alert("OK")
              this.$router.push({name: 'Login'})
          })
      },
      onInput() {

      }
  }
}
</script>

<style>
.bg-img {
    background-image: url('https://branding.ifas.ufl.edu/media/brandingifasufledu/virtual-backgrounds/Spiral.jpg');
    background-size: cover;
    background-repeat: no-repeat;
    background-position: center center;
}
</style>