<template>
  <q-page class="main-page">
    <q-tabs v-model="tabs" class="text-teal">
      <q-tab name="allBeers" icon="liquor" label="All Beers" />
      <q-tab name="myBeers" icon="sports_bar" label="My Beers" />
    </q-tabs>

    <q-separator />

    <q-tab-panels v-model="tabs" animated>
      <q-tab-panel name="allBeers">
        <div class="action-buttons">
          <q-btn :icon="gridView ? 'table_rows' : 'grid_view'" flat @click="changeLayout"
          v-if="$q.screen.gt.xs">
            <q-tooltip>
              Change Layout
            </q-tooltip>
          </q-btn>
        </div>
        <q-list>
          <beer-card
          :class="['beer-card', gridView ? 'grid-view' : '']"
          v-for="(item, index) in beers" :key="index"
          :beerData="item"
          />
        </q-list>

        <div class="load-more">
          <q-btn color="primary" label="Load More" @click="loadMore" />
        </div>
      </q-tab-panel>

      <q-tab-panel name="myBeers">
        <div class="action-buttons">
          <q-btn color="primary" @click="addCustomBeerDialog = true" class="q-mr-md">
            <q-icon left size="sm" name="add" />
            <div>Add a new beer</div>
          </q-btn>
          <q-btn :icon="gridView ? 'table_rows' : 'grid_view'" flat @click="changeLayout"
          v-if="$q.screen.gt.xs">
            <q-tooltip>
              Change Layout
            </q-tooltip>
          </q-btn>
        </div>
        <div class="custom-beers-screen" v-if="customBeers?.length > 0">
          <q-list>
            <beer-card
            :class="['beer-card', gridView ? 'grid-view' : '']"
            v-for="(item, index) in customBeers" :key="index"
            :beerData="item"
            @emitDeleteCustomBeer="deleteCustomBeer"
            />
          </q-list>
        </div>
        <div class="no-beer-screen" v-else>
          <q-item-label caption lines="2" class="text-center">
            Nothing to see yet.<br />
            <span @click="addCustomBeerDialog = true">Click here</span> to add your first beer!
          </q-item-label>
        </div>

        <q-dialog v-model="addCustomBeerDialog">
          <q-card class="add-beer-card">
            <q-card-section class="row items-center q-pb-none">
              <div class="text-h6">Add a New Beer</div>
              <q-space />
              <q-btn icon="close" flat round dense v-close-popup />
            </q-card-section>
            <q-card-section>
              <q-input dense outlined v-model="newBeerData.name" label="Beer Name *" class="q-mb-md" />

              <q-input dense outlined v-model="newBeerData.tagline" label="Genre *" class="q-mb-md" />

              <q-input
                v-model="newBeerData.description"
                dense
                outlined
                label="Description *"
                type="textarea"
              />
            </q-card-section>
            <q-card-actions align="right" class="text-primary">
              <q-btn flat label="Cancel" v-close-popup />
              <q-btn color="primary" label="Save" @click="addCustomBeer" />
            </q-card-actions>
          </q-card>
        </q-dialog>
      </q-tab-panel>

    </q-tab-panels>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue'
import BeerCard from '../components/BeerCard.vue'
import { Loading, QSpinnerPuff } from 'quasar'

export default defineComponent({
  name: 'IndexPage',
  components: { BeerCard },
  data () {
    return {
      tabs: 'allBeers',
      gridView: false,
      beers: [],
      page: 1,
      perPage: 10,
      loadingOptions: {
        QSpinnerPuff,
        spinnerColor: 'primary',
        spinnerSize: 30,
        backgroundColor: 'white'
      },
      addCustomBeerDialog: false,
      newBeerData: {
        custom: true
      },
      customBeers: []
    }
  },
  methods: {
    changeLayout () {
      this.gridView = !this.gridView
    },
    async getBeers () {
      Loading.show(this.loadingOptions)
      const res = await this.$axios.get(`/beers?page=${this.page}&per_page=${this.perPage}`)
      if (res.status === 200) {
        this.beers = res.data
      } else {
        this.$q.notify({
          message: 'Error fetching data from the server.',
          icon: 'error',
          color: 'negative'
        })
      }
      Loading.hide()
    },
    async loadMore () {
      Loading.show(this.loadingOptions)
      this.page++
      const res = await this.$axios.get(`/beers?page=${this.page}&per_page=${this.perPage}`)
      if (res.status === 200) {
        this.beers.push(...res.data)
      } else {
        this.$q.notify({
          message: 'Error fetching data from the server.',
          icon: 'error',
          color: 'negative'
        })
      }
      Loading.hide()
    },
    addCustomBeer () {
      const beers = JSON.parse(localStorage.getItem('beers')) || []
      let id
      if (beers.length > 0) {
        id = beers[beers.length - 1].id + 1
      } else {
        id = 1
      }
      const newBeerData = {
        id,
        ...this.newBeerData
      }
      if (beers?.length > 0) {
        beers.push(newBeerData)
        localStorage.setItem('beers', JSON.stringify(beers))
      } else {
        localStorage.setItem('beers', JSON.stringify([newBeerData]))
      }
      this.addCustomBeerDialog = false
      this.getCustomBeers()
      this.newBeerData = {
        custom: true
      }
    },
    getCustomBeers () {
      this.customBeers = JSON.parse(localStorage.getItem('beers'))
    },
    loadMoreCustom () {
      console.log('custom')
    },
    deleteCustomBeer (id) {
      const customBeers = JSON.parse(localStorage.getItem('beers'))
      const newBeers = customBeers.filter(item => item.id !== id)
      localStorage.setItem('beers', JSON.stringify(newBeers))
      this.$q.notify({
        message: 'Successfully deleted the custome beer.',
        icon: 'check_success',
        color: 'primary'
      })
      this.getCustomBeers()
    }
  },
  created () {
    this.getBeers()
    this.getCustomBeers()
  }
})
</script>

<style>
  .main-page{
    max-width: 1000px;
    margin: 0 auto;
  }
  .q-list{
    display: flex;
    flex-wrap: wrap;
    width: 100%;
  }
  .grid-view{
    width: 48%;
    float: left;
    margin-right: 2%;
  }
  .action-buttons{
    display: flex;
    justify-content: flex-end;
    margin-bottom: 20px;
    width: 100%;
  }
  .no-beer-screen{
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 400px;
    border: 1px solid rgba(0, 0, 0, 0.12);
    border-radius: 4px;
  }
  .no-beer-screen span{
    color: #1976D2;
    cursor: pointer;
  }
  .load-more{
    display: flex;
    justify-content: center;
    width: 100%;
    margin-top: 40px;
  }
  .add-beer-card{
    width: 600px;
    padding: 0 20px 20px;
  }
</style>
