<template>
  <div>
    <v-menu offset-y origin="center center" :nudge-bottom="10" transition="scale-transition" v-model="poped">
      <template v-slot:activator="{ on }">
        <v-btn icon v-on="on">
          <v-badge color="red" overlap 
            v-if="badge"
            key="badge"
          >
            <span slot="badge">{{badge}}</span>
            <v-icon medium>mdi-bell-outline</v-icon>
          </v-badge>
          <v-icon 
            v-else 
            key="no-badge"
            medium
          >
            mdi-bell-outline
          </v-icon>
        </v-btn>
      </template>
      <v-card class="elevation-0">
        <v-toolbar dense flat color="transparent">
          <v-toolbar-title><h4>{{$t("notification.list")}}</h4></v-toolbar-title>
        </v-toolbar>
        <v-divider></v-divider>
        <v-card-text class="pa-0">
          <v-skeleton-loader
            type="list-item-avatar-two-line, list-item-avatar-two-line, list-item-avatar-two-line, list-item-avatar-two-line"
            style="width: 300px;"
            v-if="loading"
            key="loading" 
          >
          </v-skeleton-loader>
          <v-list 
            v-else
            key="v-list" 
            two-line subheader class="pa-0"
          >
            <template v-for="item in items">
              <v-list-item :key="item.id" link
                @click="onClick(item)"
              >
                <v-list-item-avatar>
                  <v-icon 
                    v-if="item.unread"
                    key="not-read-icon"
                  >mdi-email-outline</v-icon>
                  <v-icon 
                    v-else
                    key="read-icon"
                  >mdi-email-open-outline</v-icon>
                </v-list-item-avatar>
                <v-list-item-content>
                  <v-list-item-title :class="{'font-weight-bold' : item.unread}"  v-text="item.title"></v-list-item-title>
                  <v-list-item-subtitle v-text="item.timeLabel"></v-list-item-subtitle>
                </v-list-item-content>
               </v-list-item>
               <v-divider :key="item.id + 'divider'"></v-divider>
            </template>
          </v-list>
          
          <v-btn block tile class="ma-0" @click="onVeiwAll">{{$t("notification.all")}}</v-btn>
          <v-divider></v-divider>
        </v-card-text>
      </v-card>
    </v-menu>
    <ViewDialog 
      v-model = "dialog" 
      :notification = "viewItem"
      :viewAction = "viewAction"
      :vularId = "vularId + '-view-dialog'"></ViewDialog>
  </div>
</template>

<script>
  import ViewDialog from "./ViewDialog"
  export default {
    name: 'vular-notifications',
    components: {
      ViewDialog,
    },
    props:{
      globalField: {default : "notifications"},
      queryAction: {default : null},
      viewAction: {default : null},
      viewAllAction: {default : null},
      vularId: {default: ''},
    },
    data () {
      return {
        loading:false,
        poped:false,
        items: [],
        dialog:false,
        viewItem:null,
      }
    },

    computed:{
      badge(){
        let globalField = this.globalField
        return globalField ? this.$store.state.globals[globalField] : ''
      }
    },

    created(){
      window.$bus.$on('dispatchModel', this.onDispatchModel)
      window.$bus.$on('ActionError', this.onActionError)
    },

    destroyed() {
      window.$bus.$off('dispatchModel', this.onDispatchModel)
      window.$bus.$off('ActionError', this.onActionError)
    },

    methods: {
      onDispatchModel(vularId, model){
        if(vularId == this.vularId){
          this.items = model
          this.loading = false
        }
      },

      onActionError(vularId){
        if(vularId == this.vularId){
          this.loading = false
        }
      },

      onClick(item){
        this.viewItem = item
        this.dialog = true
      },

      onVeiwAll(){
        if(this.viewAllAction){
          window.$bus.$emit('VularAction', this.viewAllAction, this.vularId)
        }
      },
    },
    watch:{
      "poped": {
        handler(val){
          if(this.queryAction && val){
            this.loading = true
            window.$bus.$emit('VularAction', this.queryAction, this.vularId)
          }
        },
        deep: true,        
      },
    } 
  }
</script>

<style>
</style>