<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated >
      <q-toolbar class="glossy">
      <div class="q-gutter-sm row items-center no-wrap ">
        <q-space/>
          <div  style="max-width: 600px">
              <q-tabs
              v-model="tab"
              inline-label
              outside-arrows
              mobile-arrows
              class="text-white shadow-2"
              dense
              indicator-color="transparent"
              active-color="white"
            >
              <q-tab name="all" label="ALL TOPICS" />
              <q-tab v-for="topic in tops" :key="topic.id" :label="topic.text" />
                
            </q-tabs>
            
          </div>

        <!--
        <q-tabs
            v-model="topic_tab"
            dense
            style="max-width: 900px"
            no-caps
            outside-arrows
            mobile-arrows
            inline-label
            class="bg-primary text-white shadow-2">
          <q-tab name="all" label="ALL TOPICS" />
          <q-tab name="flood" icon="fas fa-water" label="FLOOD" />
          <q-tab name="fire" icon="fas fa-fire" label="FIRE" />
          <q-tab name="hurricane" icon="fas fa-cloud-showers-heavy" label="HURRICANE" />
          <q-tab name="volcanic" icon="fas fa-exclamation" label="VOLCANIC" />
          <q-tab name="eathquake" icon="fas fa-globe-americas" label="EARTHQUAKE" />
        </q-tabs>
        <q-space/>

        <q-btn flat round color="white" icon="fas fa-filter" />
        <q-space/>
        <q-btn flat round color="white" icon="fas fa-sync-alt" />
        <q-space/>
        -->
        <q-btn flat round color="white" icon="fas fa-plus"  @click="create= true" dense/>
        <q-space/>
        <q-btn flat round color="white" icon="fas fa-trash"  dense/>
    
     </div>
     <div class="board">
 </div>
    
     <q-space/>

      <div class="q-gutter-sm row items-center no-wrap ">
          <q-btn round flat>
            <q-avatar size="26px">
              <img src="https://cdn.quasar.dev/img/boy-avatar.png">
            </q-avatar>
          </q-btn>
        </div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      class="bg-primary text-white"
      :mini="miniState"
      @mouseover="miniState = false"
      @mouseout="miniState = true"
      :width="200"
      :breakpoint="500"
    >
    <q-list>
         <q-item to="/" active-class="q-item-no-link-highlighting" >
          <q-item-section avatar>
          <span v-if="$q.platform.is.desktop" ><q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
        /> </span> 
          </q-item-section>
          <q-item-section>
            <q-item-label><strong>SWARMNET</strong></q-item-label>
          </q-item-section>
        </q-item>

        <q-separator color="orange" inset />
                    
  <!--   
      <q-input 
        filled bottom-slots 
        v-model="text" 
        label="Search Board" 
       
        color="white">
        <template v-slot:prepend>
          <q-icon name="search" />
        </template>
        <template v-slot:append>
          <q-icon name="close" @click="text = ''" class="cursor-pointer" />
        </template>
      </q-input>
  -->

         <!--
          <q-btn round dense flat color="white" icon="notifications">
            <q-badge color="red" text-color="white" floating>
              5
            </q-badge>
            <q-menu
            >
              <q-list style="min-width: 100px">
                <q-card class="text-center no-shadow no-border">
                  <q-btn label="View All" style="max-width: 120px !important;" flat dense
                         class="text-indigo-8"></q-btn>
                </q-card>
              </q-list>
            </q-menu>

          </q-btn>
          -->
        <q-item to="/" active-class="q-item-no-link-highlighting v-ripple">
          <q-item-section avatar>
            <q-icon name="fas fa-home"/>
          </q-item-section>
          <q-item-section>
            <q-item-label>Post Board</q-item-label>
          </q-item-section>
        </q-item>

        <q-item to="/AlertMap" active-class="q-item-no-link-highlighting">
          <q-item-section avatar>
            <q-icon name="fas fa-exclamation-triangle"/>
          </q-item-section>
          <q-item-section>
            <q-item-label>Alerts</q-item-label>
          </q-item-section>
        </q-item>

         <q-item to="/Note" active-class="q-item-no-link-highlighting">
          <q-item-section avatar>
            <q-icon name="fas fa-bell"/>
          </q-item-section>
          <q-item-section>
            <q-item-label>Notifications</q-item-label>
          </q-item-section>
        </q-item>
      
        <q-item to="/Charts" active-class="q-item-no-link-highlighting">
          <q-item-section avatar>
            <q-icon name="fas fa-chart-line"/>
          </q-item-section>
          <q-item-section>
            <q-item-label>Post Analytics</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container class="bg-grey-2">
      <router-view />
    </q-page-container>
    <q-dialog v-model="create">
      <q-card style="width: 600px; height: 200px; background-color: powderblue;">
        <q-card-section>
          <div class="text-h6">New Topic</div>
        </q-card-section>
        
        <q-card-section>
        <q-input filled  v-model="ph" placeholder="Enter Title of Topic"  />
        </q-card-section>
      
        <q-card-actions align="right">
          <q-btn flat label="Discard" color="primary" @click="text = ''" v-close-popup />
          <q-btn flat label="Post" color="primary" v-close-popup @click="triggerPositive(); text = '';"/>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink.vue'
import PostBoard from 'components/PostBoard.vue';
import { defineComponent, ref } from 'vue'
import { useQuasar } from 'quasar'
import { onMounted} from 'vue'
import { api } from 'boot/axios'


export default defineComponent({
  name: 'MainLayout',

  props:['lorem'],

  components: {
    EssentialLink,
    PostBoard
  },

  data () {
    return {
      text: '',
      tag: '',
      dialogVis: false,
      dialogPos: {
        x: 0,
        y: 0,
      },
      search: ''
    }
  },
  
  computed: {
    dialogStyle() {
      return {
        transform: `translate(${this.dialogPos.x}px, ${this.dialogPos.y}px)`
      }
    }
  },
  
  methods: {
    onPan(evt) {
      this.dialogPos = {
        x: this.dialogPos.x + evt.delta.x,
        y: this.dialogPos.y + evt.delta.y
      }
    }
  },

  setup () {
    const leftDrawerOpen = ref(false)
    const lorem = ref('')
    const $q = useQuasar()
    const tops = ref([])
    const data = ref(null)

     function loadData () {
       console.log("hello")
     
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

  onMounted(() => {
      loadData();
    })

    return {
      tops,
      loadData,
      leftDrawerOpen: ref(false),
      /*toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      },*/
      lorem,
      miniState: ref(true),
      topic_tab:ref('all'),
      maximizedToggle: ref(false),
      dense: ref(false),
        topic_tab:ref('all'),
        create: ref(false),
        ph: ref(''),

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
    }
  }
})
</script>

