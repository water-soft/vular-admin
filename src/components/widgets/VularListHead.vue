<template>
  <div>
      <v-row>
        <v-col cols="12" class="py-0">
          <div  
            :style="{
              background: $store.state.vularApp.content.card.color,
              height:tableHeaderHeight + 'px',
            }" 
            class="d-flex flex-row align-center px-6 "
            style = "box-shadow: 2px 2px 5px rgba(0,0,0,0.05);transition: height 0.3s;"
          >
            <VularListActions 
              :batchActions="batchActions"
              :filters = "filters"
              :isStick = "isStick"
              :queryAction = "queryAction"
              v-model="inputValue"
              @selectAll = "onSelectAll" 
            ></VularListActions>
          </div>
        </v-col>
      </v-row>
      <v-row
        align="center"
        justify="center"
        class="py-0" 
        v-if = "!transshape"
        :style= "{height : tableTitleHeight + 'px'}"
      >
        <v-col cols="12" justify="center" 
          class="py-0 list-title-col"
        >
          <div class="list-title-wrap px-6">
            <VularListTitle :columns="columns"></VularListTitle>
          </div>
        </v-col>
      </v-row>
  </div>
</template>

<script>
  import VularListTitle from "./VularListTitle"
  import VularListActions from "./VularListActions"
  export default {
    name: 'vular-list-head',
    components:{
      VularListTitle,
      VularListActions
    },

    props: {
      batchActions: { default: ()=>{return []} },
      filters : { default:()=>{return []} },
      columns : { default:()=>{return []} },
      canSelect: {default: true},
      transshape : {default :'false'},
      value: {default: ()=>{return []}},
      heightPercent: { default : 1 },
      queryAction:{default:null},
    },

    data () {
      return {
      }
    },

    computed:{
      inputValue: {
        get:function() {
          return this.value;
        },
        set:function(val) {
          this.$emit('input', val);
        },
      },

      isStick(){
        return this.heightPercent < 0.5
      },

      tableHeaderHeight(){
        return 50 + this.heightPercent * 40
      },

      tableTitleHeight(){
        return 40 
      },

      titleFontSize(){
        let scale = 12
        if(this.$vuetify.breakpoint.xs){
          scale = 3
        }
        if(this.$vuetify.breakpoint.sm){
          scale = 6
        }
        if(this.$vuetify.breakpoint.md){
          scale = 8
        }
        return 16 + (this.heightPercent * scale);
      }
    },

    mounted () {
      this.$emit('listHeaderHeight', this.transshape ? 90 :130)
    },

    methods: {
      onSelectAll(val){
        this.$emit("selectAll", val)
      }
    },

    watch:{
      "transshape":function(val){
        this.$emit('listHeaderHeight', val ? 90 :130)
      }
    },
  }
</script>

<style>
  .list-title-col{
    display: flex; align-items: center;
  }
  .list-title-wrap{
    width:100%; height: 40px; border-bottom:rgba(0,0,0,0.05) solid 1px; display: flex; align-items: center;
  }
 </style>