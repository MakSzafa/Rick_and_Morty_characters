<template>
  <div class="CharacterList">
    <div class="header">
      <h1>Photo</h1>
      <h1>Character ID</h1>
      <h1>Name</h1>
      <h1>Gender</h1>
      <h1>Species</h1>
      <h1>Last Episode</h1>
      <h1>Add To Favorites</h1>
    </div>
    <div v-for="character in characterList" :key="character.id" class="character-rows">
      <img :src="character.image" alt="img"/>
      <h2>{{character.id}}</h2>
      <h2>{{character.name}}</h2>
      <div class="gender">
        <i v-if="character.gender === 'Male'" class="material-icons"
           style="font-size: 20px; color: #9d9b9b; ">male</i>
        <i v-if="character.gender === 'Female'" class="material-icons"
           style="font-size: 20px; color: #9d9b9b; ">female</i>
        <i v-if="character.gender === 'unknown'" class="material-icons"
           style="font-size: 20px; color: #9d9b9b; ">remove</i>
        <i v-if="character.gender === 'Genderless'" class="material-icons"
           style="font-size: 20px; color: #9d9b9b; ">close</i>
        <h3>{{character.gender}}</h3>
      </div>
      <h2>{{character.species}}</h2>
      <h2>{{character.episode}}</h2>
      <button v-if="!character.isFav" @click="addToFavorites(character.id)" :id="`button-${character.id}`"
              class="add-button">
        <i class="material-icons" style="font-size: 30px; padding-top: 3px; color: #08B2C9;"
           :id="`star-${character.id}`">star</i></button>
      <button v-if="character.isFav" @click="removeFromFavorites(character.id)" :id="`button-${character.id}`"
              class="remove-button">
        <i class="material-icons" style="font-size: 30px; padding-top: 3px; color: white;"
           :id="`star-${character.id}`">star</i></button>
    </div>
  </div>
</template>

<script lang="ts">
import {defineComponent} from 'vue';

export default defineComponent({
  name: 'CharacterList',
  props: {
    characterList: {
      type: Array,
      required: false,
    },
  },
  methods:{
    addToFavorites(id: number){
      this.$emit('addToFavorites', id)
    },
    removeFromFavorites(id: number){
      this.$emit('removeFromFavorites', id)
    },
  },
});
</script>

<style scoped>

.CharacterList{
  margin: 20px auto;
}

.header{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
  background-color: #e9f3f5;
}
h1{
  font-size: 14px;
  color: #9d9b9b;
  margin-left: 10px;
  margin-right: 10px;
}
h2{
  font-size: 14px;
  font-weight: normal;
  color: #9d9b9b;
  margin-top: auto;
  margin-bottom: auto;
  margin-left: 10px;
}
h3{
  font-size: 14px;
  font-weight: normal;
  color: #9d9b9b;
  margin-top: auto;
  margin-bottom: auto;
  margin-left: 3px;
}
img{
  width: 60px;
  height: 60px;
  margin: auto 10px;
}
.gender{
  display: inline-flex;
  align-items: center;
  margin-left: 10px;
}
.add-button{
  width: 40px;
  height: 40px;
  outline: none;
  border: solid 2px #08B2C9;
  border-radius: 10px;
  margin: auto;
  padding: 0;
}
.remove-button{
  width: 40px;
  height: 40px;
  outline: none;
  border: solid 2px #08B2C9;
  border-radius: 10px;
  margin: auto;
  padding: 0;
  background-color: #08B2C9;
}
.character-rows{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
  border-bottom: solid 1px #9d9b9b;
  height: 80px;
}

</style>
