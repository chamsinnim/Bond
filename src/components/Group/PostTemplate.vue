<template lang="pug">
              div()
                .card()
                  .card-content
                    article.media
                      .media-left
                        figure.image.is-64x64.img-user
                          img.user-img(:src='a.author.profile_img', alt='Image')
                      .media-content
                        p.title.is-4.user-name {{ a.author.nickname }}
                        p.subtitle.is-6 {{ a.created_date }}
                      //- post삭제
                      button.delete(@click="deletePost(a.pk)")



                      //- 드롭다운 버튼
                      //- .dropdown.is-right.is-active
                        .dropdown-trigger
                          button(aria-haspopup='true', aria-controls='dropdown-menu3' @click="openDropdownPost($event)")
                            span.icon
                              i.icon-more.ion-android-more-vertical(aria-hidden='true')

                        //- #dropdown-menu3.dropdown-menu(role='menu' :class="post.pk")
                        #dropdown-menu3.dropdown-menu(role='menu' v-show="dropdownpost" :class='post.pk' ref="dropdownpostref")
                          .dropdown-content
                            ul
                              li
                                a.dropdown-item(href='#')
                                  | 글 수정
                              li
                                a.dropdown-item(href='#')
                                  | 글 삭제

                    //- 글 (최상위)
                    .content
                      | {{ a.content }}

                      
                    //- 이미지
                    .content(v-if="a.image")
                      figure.image
                        img(:src='a.post.image')


                    //- 동영상
                    //- .content(v-if=' -1 > 0')
                      figure
                        video.responsive-svg(controls='', poster='http://bulma.io/images/placeholders/480x320.png', preload='none', width='640', height='360')
                          source(src='../../assets/KakaoTalk_2017-08-02-19-43-12_Video_36.mp4', type='video/webm; codecs="vp8, vorbis"')
                          track(src='', kind='captions', srclang='en', label='English captions', default='')



                    //- 첨부파일
                    //- .content(v-if=' -1 > 0')
                      .file-box
                        a(href='#')
                          .columns.is-mobile
                            .column.is-1
                              span
                                i.fa.fa-folder-open-o
                            .column 
                              span
                                p README.md
                            .column.is-1
                              span
                                i.fa.fa-arrow-down
                  
                  
                  //- 좋아요, 댓글 개수
                  footer.card-footer
                    button(type="submit" @click="addLike(a.pk)").card-footer-item.btn-show-like
                      span.icon-like
                        i.fa.fa-heart-o(v-if="!a.is_like")
                        i.fa.fa-heart(v-else)
                        //- i.fa.fa-heart-o(v-show="!like")
                        //- i.fa.fa-heart(v-show="like")
                      | &nbsp;  
                      | {{ a.like_count }}
                    //- button(@click="showComment($event)").card-footer-item.btn-show-comment
                    button(@click="fetchCommentData(a.pk)").card-footer-item.btn-show-comment
                      | 댓글
                      | {{ a.comment_count }}
                      | &nbsp; 
                      span.icon.is-small(v-show="!showcomment")
                        i.fa.fa-angle-down(aria-hidden='true')
                      span.icon.is-small(v-show="showcomment")
                        i.fa.fa-angle-up(aria-hidden='true')
                        

                //- 댓글 영역
                .card
                  .card-content
                    //- 댓글 작성 영역
                    article.media
                      .media-content.columns.is-mobile
                        .field.column.is-10.is-3-mobile
                          .control
                            textarea.textarea.textarea-comment(placeholder='댓글을 달아주세요.' v-model="write_comment" ref="comment_area")
                        .field.column.is-2.is-1-mobile
                          .control
                            button.btn-comment.btn-default.is-hidden-mobile(type="button" @click="writeCommentSubmit(a.pk)" ) 댓글 달기
                            button.btn-comment.btn-default.is-hidden-desktop.is-hidden-tablet(type="button" @click="writeCommentSubmit(a.pk)")
                              span.icon
                                i.fa.fa-pencil
                    
                    //- 댓글 리스트 영역
                    //- article.media(v-show="showcomment" v-for="comment in comment_data" ref="togglecomment")
                    article.media(v-for="comment in comment_data")
                      figure.media-left
                        p.image.is-48x48
                          img.user-img(:src='comment.author.profile_img')
                      .media-content
                        .content
                          p
                            strong {{ comment.author.nickname }}
                            br
                            | {{ comment.content }}
                            br
                            small
                              | {{ comment.created_date }}
                      button.delete(@click="deleteComment(comment.pk, a.pk)")

                //- .columns.is-mobile.pagination-wrapper
                //-   .column.is-offset-4.is-one-third.has-text-centered
                //-     button.pagination-next.pagination-btn.is-centered(@click="nextPage()" :disabled='pagination.next === null') 더보기  

        
</template>

<script>
// import {bus} from './bus'
// import WriteModal from './WriteModal';
// import LeaveGroupModal from './LeaveGroupModal';
// import PostTemplate from './PostTemplate';
// import DeletePostModal from './DeletePostModal';

export default {
  created(){
    // this.fetchGroupData();
    // this.fetchPostData();
    // this.fetchCommentData();
    // bus.$on('add-post-data')
    // this.deletePost();
  },
  watch: {
    // deletePost(){}
  },
  props:['a'],
  data() {
    return {
      write_comment: '',
      visible: false,
      // dropdownpost: false,
      // dropdowncomment: false,
      showcomment: false,
      // like: false,
      // like_or_not: '',
      // write: {
      //   // 텍스트 내용
      //   content:'',
      //   // 그룹 pk값..임의로 정해둠
      //   group: 29
      // },
      author: {},
      // group_data:[],
      post_data:[],
      comment_data:[],
      // target: ''
      pk:'',
      page_num: '',
      pagination:{
        next: '', 
        prev: '',
        all: ''
      },
    }
  },
  components: {
    // WriteModal,
    // LeaveGroupModal,
    // PostTemplate,
    // DeletePostModal
  },
  methods: {
    // addPostData(o){
    //   console.log(this.post_data);
    //   this.post_data.unshift(o);
    //   console.log(this.post_data);
    // },
    deletePost(pk, i){
      // console.log('i',this.post_data);
      let user_token = window.localStorage.getItem('token');
      this.$http.delete('http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/' + `${pk}`+ '/',
       { headers: {'Authorization' : `Token ${user_token}`}})
                .then(response=> {
                })
                .catch(error => console.log(error.response));
    },
    // fetchPostData(direction){
    //   let user_token = window.localStorage.getItem('token');
    //   let pk = window.localStorage.getItem('this_group');
    //   let path = null;
    //   let page_num = 1;
    //   if (this.page_num.trim() === ''){
    //     path = 'http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/?group=' + `${pk}` + '&page=' +`${page_num}`
    //   }
    //   else{
    //     path = this.pagination[direction];
    //     page_num = this.page_num;
    //   }
    //   this.$http.get(path,
    //    { headers: {'Authorization' : `Token ${user_token}`} })
    //             .then(response=> {
    //               // this.post_data = response.data.results;
    //               let data = response.data.results;
    //               console.log('data',data);
    //               data.forEach(item => {
    //                 this.post_data.push(item);
    //               });
    //               console.log('profile_img',data[0].author.profile_img);
    //               this.pagination.next = response.data.next;
    //               this.pagination.prev = response.data.previous;
    //               this.$router.push({ path: '/JointGroup/', query: { page: `${page_num}` }});
    //               // console.log(response)                  
    //               // console.log('this.post_data:',this.post_data);
    //             })
    //             // .then(write => {const datalist = Object.values(write);
    //             // this.datalist = datalist;
    //             // })
    //             // 
    //             // .then(data => console.log(data))
    //             .catch(error => console.log(error.response));
    // },
    nextPage(){
      // "http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/?group=210&page=2".slice(-1) => 2
      let api_path = this.pagination.next;
      if (api_path !== null) {
      // let first = api_path.indexOf('?page=');
      // let last = api_path.indexOf('&');
      let page_path = api_path.slice(-1);
      this.page_num = page_path
      this.fetchPostData('next');
      // console.log('작동된다')
      }
    },
    writeCommentSubmit(pk){
      let user_token = window.localStorage.getItem('token');
      let user_img = window.localStorage.getItem('user_img');
      let user_nickname = window.localStorage.getItem('user_nickname');
      let comment_submit_data = {
        post: pk,
        content: this.write_comment
      }
      this.$http.post('http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/comment/', 
        comment_submit_data,
        { 
          headers: {
            'Authorization' : `Token ${user_token}`,
            // 'Content-Type': 'multipart/form-data'
          }
        })
        .then(function (response) {
            let data = response.data;
            // console.log('comment:',data);
            // this.comment_data.unshift({
            //   author: {
            //     // email: author.email,
            //     nickname: user_nickname,
            //     // pk: author.pk,
            //     profile_img: user_img,
            //     // username: author.nickname
            //   },
            //   content: data.content,
            //   created_date: data.created_date,
            //   pk: data.pk,
            //   post: data.post,
            // })
        }).catch(function (error) {
        console.error(error.message);
      });
    },
    fetchCommentData(post_pk){
      let user_token = window.localStorage.getItem('token');
      let pk = window.localStorage.getItem('this_group');
      let ppk = post_pk;
      console.log('postpk', ppk);
      let post = {
        post: ppk
      }
      this.$http.get('http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/comment/', post,
      // this.$http.get('http://bond.ap-northeast-2.elasticbeanstalk.com/api/group=' + `${pk}` + '/post=' + `${ppk}`,
       { headers: {'Authorization' : `Token ${user_token}`} })
                .then(response=> {
                  this.comment_data = response.data.results;
                  // console.log('this.comment_data:',this.comment_data);
                  console.log('comment::',response);
                })
                .catch(error => console.log(error.response));
    },
    // openDropdownPost(e) {
    //   // this.e.target
    //   // let el = this.$refs.dropdownpostref
    //   // let target = e.target
    //   // console.log(el);
    //   // console.log(target);
    //   // if(el !== target && !el.contains(target)){
    //   //   this.visible = false;
    //   // }
    //   // this.dropdownpost = !this.dropdownpost;
    // },
    // openDropdownComment(e) {
    //   this.dropdowncomment = !this.dropdowncomment;
    // },
    showComment(e) {
      let el = this.$refs.togglecomment
      let target = e.target
      console.log(el);
      console.log(target);
      if(el !== target && !el.includes(target)){
        // this.visible = false;
      this.showcomment = !this.showcomment;
      }
    },
    addLike(pk) {
      let user_token = window.localStorage.getItem('token');
      // console.log('pk:',pk);
      // console.log('token:',user_token);
      // /api/post/<pk>/post-like-toggle
      this.$http.post('http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/' + `${pk}`+ '/post-like-toggle/', true,
       { headers: {'Authorization' : `Token ${user_token}`}})
                .then(response=> {
                  // console.log('like.response:',response);
                  let data = response.data;
                  // this.post.like_count
                })
                .catch(error => console.log(error.response));
      // this.like = !this.like;
    },
    deleteComment(pk,ppk){
      // console.log(pk);
      // console.log(ppk);
      let post = {
        post: ppk
      }
      let user_token = window.localStorage.getItem('token');
      this.$http.delete('http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/comment/' + `${pk}` + '/',
      { headers: {'Authorization' : `Token ${user_token}`}})
      .then(response => {
        this.$http.get('http://bond.ap-northeast-2.elasticbeanstalk.com/api/post/comment/', post,
        { headers: {'Authorization' : `Token ${user_token}`}})
        .then(response=> {
          this.comment_data = response.data.results;
            // console.log('this.comment_data:',this.comment_data);
            console.log('comment::',response);
          })
          .catch(error => console.log('get-error:',error.response));
        console.log(response);
        })
      .catch(error => console.log('delete-error:',error.response));
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
.user-img
  background: #eee

body
  background: #eee

.page-wrapper
  min-height: 87vh

.card-wrapper
  margin-bottom: 20px


.icon-more
  font-size: 1.5rem
  color: $grey
  &:hover
    color: $bond
  &:focus
    color: $bond
  &:active
    color: $bond



.navbar-burger.burger
  padding-top: 8px
  padding-left: 10px

.dropdownhr
  margin: 5px

.btn-show-comment,
.btn-show-like
  font-size: 1rem
  color: $primary

.fa-heart,
.fa-heart-o
  font-size: 1rem
  margin-top: 1px



</style>
