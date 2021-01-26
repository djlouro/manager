<template>
  <div>
                <CRow>
                    <CCol lg="3" md="3" class="border-right" style="min-width: 220px" xl="2">
                        <h2>Topics</h2>
                        <div class="m-3 cursor-pointer" @click="currentCategory = 'general'; getForumData()" >
                            <CIcon name="cil-star" class="mb-1 text-info"/>
                            General
                        </div>
                        <div class="m-3 cursor-pointer" @click="currentCategory = 'weather'; getForumData()" >
                            <CIcon name="cil-star" class="mb-1 text-primary"/>
                            Weather
                        </div>
                        <div class="m-3 cursor-pointer" @click="currentCategory = 'random'; getForumData()" >
                            <CIcon name="cil-star" class="mb-1 text-warning"/>
                            Random
                        </div>
                        <div class="m-3 cursor-pointer" @click="currentCategory = 'suggestions'; getForumData()">
                            <CIcon name="cil-star" class="mb-1 text-success"/>
                            Suggestions
                        </div>
                        <div class="m-3 cursor-pointer" @click="currentCategory = 'problems'; getForumData()">
                            <CIcon name="cil-star" class="mb-1 text-danger"/>
                            Problems
                        </div>
                    </CCol>
                    <CCol v-if="!questionOpened">
                      <div v-for="item in questions" :key="item.index">
                        <transition name="fade">
                            <CCard :accent-color="currentCategoryColor">
                                <CCardHeader class="cursor-pointer" @click="isCollapsed == item.id ? isCollapsed = 0 : isCollapsed = item.id">
                                    <span class="font-xl">{{item.topicTitle}}</span>
                                    <span class="mx-4 text-right text-muted">Posted by: {{item.topicOwner}}</span>
                                </CCardHeader>
                                <CCollapse :show="isCollapsed == item.id" :duration="400">
                                    <CCardBody>
                                        <p>{{item.topicBody}}</p>
                                        <CButton size="sm" color="primary" @click="openQuestion(item)">See this topic</CButton>
                                    </CCardBody>
                                </CCollapse>
                            </CCard>
                        </transition>
                      </div>
                      <CDropdownDivider />
                      <span>
                          Post topic:
                      </span>
                      <CInput v-model="postTitle"></CInput>
                      <CTextarea v-model="postBody"></CTextarea>
                      <CButton size="sm" color="primary" @click="postNewTopic()">Post</CButton>
                    </CCol>
                    <CCol v-else> 
                      <span class="font-4xl">{{currentQuestion.topicTitle}}</span>
                      <span class="mx-4 text-muted">Posted by: {{currentQuestion.topicOwner}}</span>
                      <p class="font-lg my-3">{{currentQuestion.topicBody}}</p>
                      <CDropdownDivider />
                      <div v-for="item in currentQuestion.topicComments" :key="item.index">
                            <CCard :accent-color="currentCategoryColor">
                                <CCardHeader class="cursor-pointer">
                                    <span class="mx-4 text-muted">Posted by: {{item.commentOwner}}</span>
                                </CCardHeader>
                                    <CCardBody class="m-0 p-3">
                                        <p>{{item.commentBody}}</p>
                                    </CCardBody>
                            </CCard>
                      </div>
                      <CDropdownDivider />
                      <span>
                          Post comment:
                      </span>
                      <CTextarea v-model="commentBody"></CTextarea>
                      <CButton size="sm" color="primary" @click="postNewComment()">Post</CButton>
                    </CCol>
                </CRow>
  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'

export default {
  name: 'Forum',
  data () {
    return {
        currentCategory: 'general',
        currentCategoryColor: 'info',
        forumData: [],
        questions: [],
        currentQuestion: [],
        questionOpened: false,
        isCollapsed: false,
        postTitle: '',
        postBody: '',
        commentBody: ''
    }
  },
  computed: {
    
  },
  watch: {
    currentCategory() {
      switch(this.currentCategory) {
        case 'general':
          this.currentCategoryColor = 'info'
          this.questionOpened = false
          break;
        case 'weather':
          this.currentCategoryColor = 'primary'
          this.questionOpened = false
          break;
        case 'random':
          this.currentCategoryColor = 'warning'
          this.questionOpened = false
          break;
        case 'suggestions':
          this.currentCategoryColor = 'success'
          this.questionOpened = false
          break;
        case 'problems':
          this.currentCategoryColor = 'danger'
          this.questionOpened = false
          break;
        default:
          this.currentCategoryColor = 'info'
          this.questionOpened = false
      } 
    }
  },
  methods: {
    postNewComment() {
        this.currentQuestion.topicComments.push(
          {
            "commentOwner": this.$store.state.username,
            "commentBody": this.commentBody
          }
        )
        axios({
          method: "PUT",
          url: "http://localhost:3000/"+ this.currentCategory+"/" + this.currentQuestion.id,
          data: this.currentQuestion
        }).then(r => {
          this.getForumData()
        })
    },
    postNewTopic() {
      let dataToSend = {
          "topicTitle": this.postTitle,
          "topicBody": this.postBody,
          "topicOwner": this.$store.state.username,
          "topicComments": []
        }
      axios({
        method: "POST",
        url: "http://localhost:3000/"+this.currentCategory,
        data: dataToSend
      }).then(r => {
        this.getForumData()
    })
    },
    openQuestion(item) {
      this.questionOpened = true
      this.currentQuestion = item
    },
    getForumData() {
      axios({
          method: 'GET',
          url: 'http://localhost:3000/'+this.currentCategory
    }).then(r => {
      console.log(r)
        this.forumData = r.data
        this.questions=r.data
        //this.setQuestions()
      })
    },
    setQuestions(topic) {
      console.log(this)
      for (let i = 0; i < this.forumData.length; i++) {
        console.log(this.forumData[i].topic)
        if (this.forumData[i].topic === topic) {
          console.log(this.forumData[i].posts)
          this.questions = this.forumData[i].posts
        }
      }
    }
  },
  created() {
    this.getForumData()
  }
}
</script>

<style>
</style>