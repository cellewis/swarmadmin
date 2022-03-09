<template>
<q-list separator>
        <transition-group
          appear
          enter-active-class="animated fadeIn slow"
          leave-active-class="animated fadeOut slow"
        >
          <q-item
            v-for="qweet in qweets"
            :key="qweet.id"
            class="qweet q-py-md"
          >
            <q-item-section avatar top>
              <q-avatar size="xl">
                <img src="https://cdn.quasar.dev/img/boy-avatar.png">
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label class="text-subtitle1">
                <strong>Fire</strong>
                <span class="text-grey-7">
                  #Fire
                  <br class="lt-md">&bull; 14 hours ago
                </span>
              </q-item-label>
              <q-item-label class="qweet-content text-body1">{{ qweet.content }}</q-item-label>
              <div class="qweet-icons row justify-between q-mt-sm">
                <q-btn
                  color="grey"
                  icon="far fa-comment"
                  size="sm"
                  flat
                  round
                />
                <q-btn
                  color="grey"
                  icon="fas fa-eye"
                  size="sm"
                  flat
                  round
                />
                <q-btn
                  :color="qweet.liked ? 'grey' : 'grey'"
                  :icon="qweet.liked ? 'fas fa-heart' : 'far fa-heart'"
                  size="sm"
                  flat
                  round
                />
              </div>
            </q-item-section>
          </q-item>
        </transition-group>
      </q-list>
  <!-- save for later
    <div class="q-pa-md" >
    <div class="q-gutter-md row items-start">
      <q-select
        color="pink"
        filled
          v-model="model"
          map-options
          emit-value
          option-value="id"
          option-label="text"
          :options="tops"
        label="Select a topic: "
        style="width: 250px"
      />

      <q-btn flat round color="primary" icon="fas fa-filter" @click="getDetails" />
      <q-btn flat round color="primary" icon="fas fa-sync-alt" />
    </div>
    </div>-->


   <!-- <div class="q-pa-md" >
    <q-list bordered padding separator>
      <q-item 
      v-for="post in pos" 
      :key="post.id" 
      :to="`/Details/${post.id}`" 
      active-class="q-item-no-link-highlighting">
      
        <q-item-section top avatar>
          <q-avatar>
            <img src="https://cdn.quasar.dev/img/boy-avatar.png">
          </q-avatar>
        </q-item-section>

        <q-item-section>
          <q-item-label> <strong> {{post.text}} </strong></q-item-label>

          <div class="row justify-between q-mt-sm">
                <q-btn flat round color="grey" icon="fas fa-comments" size="sm" />
                <q-btn flat round icon="far fa-eye" size="sm"/>
                <q-btn flat round icon="far fa-heart" size="sm" />
                <q-btn-dropdown  flat icon="fas fa-hashtag" size="sm">
                    <q-list separator>
                      <q-item  v-close-popup 
                                v-for="tag in posTags" 
                                :key="tag" >
                        <q-item-section >
                          <q-item-label>{{tag.text}}</q-item-label>
                        </q-item-section>
                      </q-item>
                    </q-list>
                  </q-btn-dropdown>
                
          </div> 
        </q-item-section>

        <q-item-section side top>
          <q-item-label caption> {{post.created}} </q-item-label>
        </q-item-section>
        
       
      </q-item>
    </q-list>

    <br>
    
    </div>
-->

    <q-page-sticky position="bottom-right" :offset="[2, 80]">
      <q-toggle
        v-model="subbed"
        checked-icon="check"
        color="red"
        unchecked-icon="clear"
        v-on:click="showNotif"
      />
      
    </q-page-sticky>

    <q-page-sticky position="bottom-right" :offset="[5, 18]">
      <q-btn fab icon="far fa-edit" color="accent" @click="fixed = true"/>
    </q-page-sticky>

    <q-dialog v-model="fixed">
      <q-card style="width: 600px; height: 400px; background-color: powderblue;">
        <q-card-section>
          <div class="text-h6">NEW DISCUSSION</div>
        </q-card-section>
        
        <q-card-section>
        <q-input filled  v-model="ph" placeholder="Enter Title of Post"  />
        </q-card-section>
      
        <q-separator />

        
        <q-card-section style="height: 200px" class="scroll" counter maxlength="260">
          <q-input type="textarea" v-model="text" counter maxlength="260"  autogrow>
          </q-input>
        </q-card-section>

        <q-separator />

        <q-card-actions align="right">
          <q-btn flat label="Discard" color="primary" @click="text = ''" v-close-popup />
          <q-btn flat label="Post" color="primary" v-close-popup @click="triggerPositive(); text = '';"/>
        </q-card-actions>
      </q-card>
    </q-dialog>


</template>

<script>
import {defineComponent, defineAsyncComponent, ref} from 'vue';
import { api } from 'boot/axios'
import { useQuasar } from 'quasar'
import { onMounted} from 'vue'


export default defineComponent({
  name: 'PostBoard',

  props: ['tpost'],

  data(){
    return{
      newPostContent:'',
      posts:[
        {
          content:'Update your Disaster Preparedness Kit',
          date:1643636383048
        },
        {
          content:'Gone with the Wind: 318 Roof Destroyed !',
          date:1643636526005
        }
      ],
      newQweetContent: '',
      qweets: [
        {
           id: 'ID1',
           content: 'Fire is very hot.',
           date: 1611653238221,
           liked: false
         },
         {
           id: 'ID2',
           content: 'Fire is bad.',
           date: 1611653252444,
           liked: true
         },
      ]
    }
  },
  methods:{
    addNewPost() {
      let newTweet = {
        content: this.newPostContent,
        date: Date.now(),
      };
      this.tweets.unshift(newTweet);
      this.newPostContent = "";
    },
  }, 
  setup () {
    const $q = useQuasar()
    const data = ref(null)
    const tops = ref([])
    const pos = ref([])
    const  subbed = ref(true)
    const model = ref(null)
    const posTags = ref([])

  function loadData () {
    api.get('https://swarmnet-staging.herokuapp.com/topics',{
  method: 'GET',
  
  headers: {
          'Access-Control-Allow-Origin': '*'
        }
    })
      .then((response) => {
        data.value = response.data
        
        for (let i of data.value) { 
          tops.value.push(i)
         
        }
      
      /* console.log( tops.value[0].text)
       console.log(tops) */
      })
      .catch(() => {
        $q.notify({
          color: 'negative',
          position: 'top',
          message: 'Loading failed',
          icon: 'report_problem'
        })
      })
  }

  function getDetails(topic){
         let url = "https://swarmnet-staging.herokuapp.com/posts/" + model.value.toString()
         //let url = "https://swarmnet-staging.herokuapp.com/posts/" + toString(topic)
         console.log(url)
          api.get(url,{
          method: 'GET',
          
          headers: {
                  'Access-Control-Allow-Origin': '*'
                }
            })
          .then((response) => {
            data.value = response.data
            console.log(data.value)
            pos.value.push(data.value) 

            posTags.value = pos.value.tags
            console.log(pos.value.tags)
            
           /* for (let i of data.value) { 
              pos.value.push(i)
            
            } */
          
          console.log( "hi")
          console.log(pos) 
          })
          .catch(() => {
            $q.notify({
              color: 'negative',
              position: 'top',
              message: 'Loading failed',
              icon: 'report_problem'
            })
          })

      }

  function  showNotif () {
         if (subbed.value == true){
           $q.notify({
          message: 'SUBSCRIBBED',
          color: 'primary',
          avatar: 'https://cdn.quasar.dev/img/boy-avatar.png',
          actions: [
            { label: 'Dismiss', color: 'white', handler: () => { /* ... */ } }
          ]
        })
         }
        
       }

  onMounted(() => {
      loadData();
    })

    return {
        data, 
        loadData,
        tops,
        pos,
        posTags,
        getDetails,
        
        model,
        subbed,
        fixed: ref(false),
        text: ref(''),
        ph: ref(''),
        dense: ref(false),
        topic_tab:ref('all'),
       showNotif,


      triggerPositive () {
        $q.notify({
          type: 'positive',
          message: 'DISCUSSION POSTED'
        })
        // simulate delay
        setTimeout(() => {
          notif({
            type: 'positive',
            message: 'Found the results that you were looking for',
            timeout: 1000
          })
        }, 4000)
      },
       filters: {
    relativeDate(value) {
      return formatDistance(value, new Date())
    }
  },
      
    }
  }
})
</script>
<style lang="sass">
.new-qweet
  textarea
    font-size: 19px
    line-height: 1.4 !important
.divider
  border-top: 1px solid
  border-bottom: 1px solid
  border-color: $grey-4
.qweet:not(:first-child)
  border-top: 1px solid rgba(0, 0, 0, 0.12)
.qweet-content
  white-space: pre-line
.qweet-icons
  margin-left: -5px
</style>