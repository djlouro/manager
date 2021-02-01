<template>
  <div class="c-app flex-row align-items-center bg-img">
      <CContainer>
            <CRow class="justify-content-center">
                <CCol lg="5" md="6" sm="8">
                    <CCardGroup>
                        <CCard class="p-4 shadow-lg">
                            <CCardBody>
                                <h1>Login</h1>
                                <p class="text-muted">Sign In to your account</p>
                                <CForm>
                                     <CInput v-model="username"
                                            placeholder="Username"
                                            :disabled="completeNewPassword"
                                            @input="onInput()">
                                        <template #prepend-content>
                                            <CIcon name="cil-user"/>
                                        </template>
                                    </CInput>
                                    <CInput v-model="password"
                                            type="password"
                                            placeholder="Password"
                                            :disabled="completeNewPassword"
                                            @input="onInput()">
                                        <template #prepend-content>
                                            <CIcon name="cil-lock-locked"/>
                                        </template>
                                    </CInput>
                                    <div v-if="completeNewPassword">
                                        <p class="text-muted">
                                            Used passowrd was temporary. Set new password below.
                                        <p/>
                                        <CInput v-model="newPassword"
                                                type="password"
                                                placeholder="New password"
                                                @input="onInput()">
                                            <template #prepend-content>
                                                <CIcon name="cil-user"/>
                                            </template>
                                        </CInput>
                                        <CInput v-model="newPassword2"
                                                type="password"
                                                placeholder="Repeate new password"
                                                @input="onInput()">
                                            <template #prepend-content>
                                                <CIcon name="cil-lock-locked"/>
                                            </template>
                                        </CInput>
                                    </div>
                                    <span class="text-danger">
                                        {{errorMsg}}
                                    </span>
                                    <CRow>
                                        <CCol v-if="!completeNewPassword" col="6" class="text-left">
                                            <CButton color="primary" class="px-4" @click="signIn()">
                                                Login
                                                <CSpinner v-if="isLoading" size="sm"></CSpinner>
                                            </CButton>
                                        </CCol>
                                        <CCol v-if="completeNewPassword" col="6" class="text-left">
                                            <CButton color="primary" class="px-4" @click="signInNewPassword()">
                                                Save new passowrd
                                                <CSpinner v-if="isLoading" size="sm"></CSpinner>    
                                            </CButton>
                                        </CCol>
                                        <CCol col="6" class="text-right">
                                            <CButton color="link" class="px-0" to="/password">Forgot password?</CButton>
                                            <CButton color="link" class="px-0" to="/register">Register now!</CButton>
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
  name: 'Login',
  data () {
    return {
        isLoading: false,
        completeNewPassword: false,
        errorMsg: '',
        user: null,
        username: '',
        password: '',
        newPassword: '',
        newPassword2: '',
    }
  },
  computed: {
    
  },
  methods: {
      signIn() {
          this.isLoading = true
          Auth.signIn(this.username, this.password)
          .then(data => {
               this.isLoading = false
              if (data.challengeName === "NEW_PASSWORD_REQUIRED") {
                  this.user = data
                  this.completeNewPassword = true
              }
              else {
                this.$store.state.username=data.username
                this.$store.state.userGroup= data.signInUserSession.idToken.payload['cognito:groups'] ?
                data.signInUserSession.idToken.payload['cognito:groups'][0] : 'USER'
                this.$router.push({name: 'Dashboard'})
              }
          })
          .catch(err => {
               this.isLoading = false
              this.errorMsg = err.message
          })
      },
      signInNewPassword() {
          this.isLoading = true
            Auth.completeNewPassword(this.user, this.newPassword)
            .then(data => {
                this.isLoading = false
                this.$store.state.username=data.username
                this.$store.state.userGroup= data.signInUserSession.idToken.payload['cognito:groups'] ?
                data.signInUserSession.idToken.payload['cognito:groups'][0] : 'USER'
                this.$router.push({name: 'Dashboard'})
            })
            .catch(err => {
               this.isLoading = false
              this.errorMsg = err.message
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