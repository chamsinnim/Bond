<template lang="pug">
  div.container.page-wrapper
    .columns
      //- 그룹 정보 영역
      .column.is-3
        .card
          .card-image
            figure.image.group_profile-wrapper.is-desktop-16by9.is-mobile-1by1.is-tablet-2by1
              img.group_profile_img(:src='group_data.profile_img', alt='Image')
          .card-content
            article.media
              .media-content
                p.title.is-4 {{ group_data.name }}
                div
                  span 멤버 {{ group_data.num_of_members }}
                  | &nbsp; ·&nbsp;
                  //- a(aria-label="open leave group modal" @click.prevent="deletegroup") 
                  //-   span.icon.is-small
                  //-     i.fa.fa-cog(aria-hidden='true')
                  //-   | 그룹 삭제
                  a(aria-label="open leave group modal" @click.prevent="openLeaveGroupModal") 
                    span.icon.is-small
                      i.fa.fa-cog(aria-hidden='true')
                    | 그룹 탈퇴
            .content {{ group_data.description }}



      .column.is-9

        .feed-box
          .card
            header.card-header
              .card-header-title
                | &nbsp;  
                | &nbsp;  
                | 그룹 이름
                | &nbsp; 
                //- | &nbsp; 
                //- | &nbsp; 
                //- | &nbsp; 
                button.btn-default.column.is-offset-7.is-hidden-mobile(@click="openModal") + 멤버 초대
                button.btn-default.column.is-offset-4.is-hidden-desktop.is-hidden-tablet(@click="openModal") + 멤버 초대
              
            .card-content
              table.table.is-fullwidth
                //- col(width="20%")
                //- col(width="20%")
                //- col(width="60%")
                caption.a11y-hidden 그룹멤버
                thead
                  tr
                    th 멤버
                    th
                    

                    th 
                    //-   a
                    //-     span.icon
                    //-       i.fa.fa-angle-down

                tbody
                  tr
                    //- th 1
                    td
                      figure.image.is-48x48.img-user
                        img.user-img(src='http://bulma.io/images/placeholders/96x96.png', alt='Image')
                    td 
                      p.namelist 만순이
                    
                    td
                      span.tag.is-rounded.is-primary 리더
                  //- tr
                  //-   //- th 1
                  //-   td
                  //-     figure.image.is-48x48.img-user
                  //-       img.user-img(src='http://bulma.io/images/placeholders/96x96.png', alt='Image')
                  //-   td 
                  //-     p.namelist 만순이
                    
                  //-   td
                  //-     span.tag.is-rounded.is-primary 리더
                  //- tr
                  //-   //- th 1
                  //-   td
                  //-     figure.image.is-48x48.img-user
                  //-       img.user-img(src='http://bulma.io/images/placeholders/96x96.png', alt='Image')
                  //-   td 
                  //-     p.namelist 만순이
                    
                  //-   td
                  //-     span.tag.is-rounded.is-primary 리더
                  //- tr
                  //-   //- th 1
                  //-   td
                  //-     figure.image.is-48x48.img-user
                  //-       img.user-img(src='http://bulma.io/images/placeholders/96x96.png', alt='Image')
                  //-   td 
                  //-     p.namelist 만순이
                    
                  //-   td
                  //-     span.tag.is-rounded.is-primary 리더
                  //- tr
                  //-   //- th 1
                  //-   td
                  //-     figure.image.is-48x48.img-user
                  //-       img.user-img(src='http://bulma.io/images/placeholders/96x96.png', alt='Image')
                  //-   td 
                  //-     p.namelist 만순이
                    
                  //-   td
                  //-     span.tag.is-rounded.is-primary 리더
          invitation-modal(
            ref="my_modal"
            close_message="close lightbox"
          )
          leave-group-modal(close_message="close lightbox" ref='leave_group_modal')
</template>

<script>
import InvitationModal from './InvitationModal'
import LeaveGroupModal from './LeaveGroupModal'

export default {
  components:{
    InvitationModal,
    LeaveGroupModal
  },
  created(){
    this.fetchGroupData();
    // this.fetchPostData();
    // this.fetchCommentData();
    // bus.$on('add-post-data')
    // this.deletePost();
  },
  data() {
    return{
      visible: false,
      group_data:[],
      pk:''
    }
  },  
  methods: {
    openModal(){
      this.$refs.my_modal.visible = true;
    },
    openLeaveGroupModal(){
      this.$refs.leave_group_modal.visible = true;
    },
    fetchGroupData(){
      let user_token = window.localStorage.getItem('token');
      let pk = window.localStorage.getItem('this_group');
      this.$http.get('http://bond.ap-northeast-2.elasticbeanstalk.com/api/group/' + `${pk}`+ '/',
       { headers: {'Authorization' : `Token ${user_token}`}})
                .then(response=> {
                  this.group_data = response.data;
                  // console.log('this.group_datalist:',this.group_data);
                  // console.log('response:',response);
                })
                .catch(error => console.log(error.response));
    },
    deletemembership(){
          let pk = parseInt(window.localStorage.getItem('this_group'),10);
          console.log(pk)
          let user_token = window.localStorage.getItem('token');
          console.log(user_token)
          this.$http.delete('http://bond.ap-northeast-2.elasticbeanstalk.com/api/member/membership/',{group:pk},
                  { headers: {'Authorization' : `Token ${user_token}`}},
                  )
                  .then(response => {
                    console.log(response)
                    // this.$router.push({ path: '/NoneJointGroupFeed/', query: { group: `${pk}` }});
                  })
                  .catch(error =>{
                    console.error(error.response)
                    if(error.response.status === 401)
                    alert(error.response.data.detail)
                  })
    },
  }
}
</script>

<style lang="sass" scoped>
@import "~bulma"
@import "~style"

.group_profile-wrapper
  width: auto
  height: auto
  min-height: 100px
  max-height: 135px
  overflow: hidden
.group_profile_img
  background: url('http://bulma.io/images/placeholders/1280x960.png')
  // overflow: hidden

body
  background: #eee

.page-wrapper
  min-height: 87vh

.user-img
  background: #eee

.namelist,
  padding-top: 13px
.tag.is-rounded
  margin-top: 13px

</style>
