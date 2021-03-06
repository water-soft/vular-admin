<template>
  <v-card
    class="mt-5"
    flat
    :color="$store.state.vularApp.content.card.color" 
    :style="$store.state.vularApp.content.card.style"
  >
    <v-toolbar flat color="transparent">
      <v-toolbar-title>{{title}}</v-toolbar-title>

      <v-spacer></v-spacer>

    </v-toolbar>
    <v-divider ></v-divider>
    <v-card-text class="pa-0">
      <v-data-table
        :headers="headers"
        :items="desserts"
        hide-default-footer
        sort-by="calories"
        style="background: transparent"
      >
        <template v-slot:item.image="{ item }">
          <v-img
            aspect-ratio="1"
            :src="item.image"
            width="50"
            class="ma-2"
          >
          </v-img>
        </template>  
        <template v-slot:item.actions="{ item }">
          <div class="py-2">
            <v-icon
              small
              class="mr-2"
              @click="editItem(item)"
            >
              mdi-pencil
            </v-icon>
            <v-icon
              small
              @click="deleteItem(item)"
            >
              mdi-delete
            </v-icon>
          </div>
        </template>
        <template v-slot:no-data>
          <v-btn color="primary" @click="initialize">Reset</v-btn>
        </template>
      </v-data-table>
    </v-card-text>
    <v-divider></v-divider>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn 
        icon
        color="primary" @click="dialog = true">
        <v-icon>mdi-plus</v-icon>
      </v-btn>
      <v-spacer></v-spacer>
    </v-card-actions>
    <v-dialog v-model="dialog" max-width="500px">
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.name" label="Dessert name"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.calories" label="Calories"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.fat" label="Fat (g)"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.carbs" label="Carbs (g)"></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.protein" label="Protein (g)"></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions class="pa-5 media-alt-dialog-actions">
                  <v-spacer></v-spacer>
                  <v-btn class="mr-5" outlined rounded @click="close">{{$t('media.cancel')}}</v-btn>
                  <v-btn color="primary" class="mr-5" rounded @click="save">{{$t('media.confirm')}}</v-btn>
                </v-card-actions>
              </v-card>
    </v-dialog>
  </v-card>
</template>

<script>
  export default {
    props:{
      title:{default:''},
    },
    data: () => ({
      name:"vular-has-many-table-card",
      dialog: false,
      headers: [
        {
          text:'',
          value:'image',
          sortable: false,
          width:'50px',
        },
        {
          text: '名称',
          align: 'start',
          value: 'name',
        },
        { text: '数量', value: 'calories' },
        { text: '价格', value: 'fat' },
        { text: '库存', value: 'carbs' },
        { text: 'SKU', value: 'protein' },
        { text: '操作', value: 'actions', sortable: false, width:100 },
      ],
      desserts: [],
      editedIndex: -1,
      editedItem: {
        name: '',
        calories: 0,
        fat: 0,
        carbs: 0,
        protein: 0,
      },
      defaultItem: {
        name: '',
        calories: 0,
        fat: 0,
        carbs: 0,
        protein: 0,
      },
    }),
    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      },
    },
    watch: {
      dialog (val) {
        val || this.close()
      },
    },
    created () {
      this.initialize()
    },
    methods: {
      initialize () {
        this.desserts = [
          {
            image: '/images/1.jpg',
            name: '白色',
            calories: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
          },
          {
            image: '/images/2.jpg',
            name: '红色',
            calories: 237,
            fat: 9.0,
            carbs: 37,
            protein: 4.3,
          },
          {
            image: '/images/3.jpg',
            name: '黄色',
            calories: 262,
            fat: 16.0,
            carbs: 23,
            protein: 6.0,
          },
          {
            image: '/images/4.jpg',
            name: '银色',
            calories: 305,
            fat: 3.7,
            carbs: 67,
            protein: 4.3,
          },
          {
            image: '/images/5.jpg',
            name: '金色',
            calories: 356,
            fat: 16.0,
            carbs: 49,
            protein: 3.9,
          },
        ]
      },
      editItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },
      deleteItem (item) {
        const index = this.desserts.indexOf(item)
        confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
      },
      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },
      save () {
        if (this.editedIndex > -1) {
          Object.assign(this.desserts[this.editedIndex], this.editedItem)
        } else {
          this.desserts.push(this.editedItem)
        }
        this.close()
      },
    },
  }
</script>