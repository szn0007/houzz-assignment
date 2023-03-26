<template>
  <q-item clickable v-ripple>
    <q-item-section class="beer-card__image">
      <img :src="beerData.image_url" v-if="beerData.image_url" />
      <img src="~assets/houzz-beer.png" v-else />
      <q-tooltip anchor="top middle" self="bottom middle" :offset="[10, 10]"
      v-if="beerData.ingredients">
        {{ Object.keys(beerData?.ingredients).join(', ') }}
      </q-tooltip>
    </q-item-section>

    <q-item-section class="beer-card__content">
      <div class="beer-title">{{ beerData.name }}</div>
      <q-item-label lines="1" class="beer-tagline">{{ beerData.tagline }}</q-item-label>
      <q-item-label caption class="beer-description">
        {{ beerData.description }}
      </q-item-label>
    </q-item-section>

    <q-item-section side top v-if="beerData.custom">
      <q-icon name="delete" color="negative" @click="$emit('emitDeleteCustomBeer', beerData.id)" />
    </q-item-section>
  </q-item>
</template>
<script>
export default {
  props: {
    beerData: Object
  }
}
</script>
<style>
.beer-card{
  margin-bottom: 20px;
  border: 1px solid rgba(0, 0, 0, 0.12);
  border-radius: 4px;
  padding: 32px;
  width: 100%;
}
.beer-card img{
  max-width: 120px;
  aspect-ratio: 1;
  object-fit: contain;
}
.beer-card__image{
  max-width: 150px;
}
.beer-title{
  font-size: 32px;
  font-weight: 600;
}
.beer-tagline{
  color: #E2BD76;
  font-weight: 500;
  margin: 4px 0;
}
</style>
