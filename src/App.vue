<script setup>
import { ref } from "vue";
import { items } from "./movies.json";
/*
This is an Icon that you can use to represent the stars if you like
otherwise you could just use a simple ⭐️ emoji, or * character.
*/
import { StarIcon, TrashIcon, PencilIcon } from "@heroicons/vue/24/solid";

const movies = ref(items);
const showForm = ref(false);
const formValues = ref({
  id: 0,
  name: "",
  description: "",
  genres: [],
  rating: 0,
  inTheaters: false
})

function setRating(item, rating) {
  if (item.rating === rating) return; // Good practice to avoid unnecessary re-renders
  item.rating = rating;
}

function validFormData() {
  if (!formValues.value.name) {
    alert("Preencha o nome do filme.");
    return false;
  }
  if (!formValues.value.genres.length) {
    alert("Informe o(s) gênero(s) do filme.")
    return false;
  }
  return true;
}

function resetForm() {
  formValues.value = {
    id: 0,
    name: "",
    description: "",
    genres: [],
    rating: 0,
    inTheaters: false
  };
  showForm.value = false;
}

function saveMovie() {
  if (!validFormData()) return;
  let existingMovie = movies.value.find(movie => movie.id == formValues.value.id);
  if (existingMovie) existingMovie = { ...formValues.value };
  else
    movies.value.push({
      "id": movies.value.reduce((nextId, movie) => nextId = Math.max(nextId, movie.id), 0) + 1,
      "name": formValues.value.name,
      "description": formValues.value.description,
      "image": formValues.value.image,
      "rating": formValues.value.rating,
      "genres": formValues.value.genres,
      "inTheaters": formValues.value.inTheaters
    })

  resetForm()
}

function deleteMovie(movieId) {
  const confirmDelete = confirm("Are you sure you want to delete this movie?");
  if (!confirmDelete) return;
  movies.value = movies.value.filter(movie => movie.id != movieId);
}

function editMovie(movie) {
  formValues.value = movie;
  showForm.value = true;
}
</script>

<template>
  <div style="background-color: midnightblue; height: 100%;">
    <div class="container">
      <template
        v-for="item in movies"
        :key="item.id"
        class="flex flex-col"
      >
        <div class="movie">
          <div class="posterContainer">
            <img
              :src="item.image"
              :alt="item.name"
              class="poster"
            />
            <StarIcon :class="[item.rating ? 'text-yellow-400' : 'text-gray-400', 'h-12 w-12', 'top-right']" />
            <span style="color: black; font-size: 1.5rem; position: absolute; top: 21px; right: 34px;">
              {{ item.rating ?? '-' }}
            </span>
          </div>
          <ul style="padding: 5px;">
            <li>
              <h2><b>{{ item.name }}</b></h2>
              <div
                v-for="genre in item.genres"
                style="display: inline-block"
              >
                <div class="genre">{{ genre }}</div>
              </div>
            </li>
            <li>{{ item.description }}</li>
            <li>
              <div
                v-for="star in 5"
                class="stars"
              >
                <StarIcon
                  :class="[star <= item.rating ? 'text-yellow-400' : 'text-gray-400', 'h-5 w-5']"
                  style="cursor: pointer;"
                  @click="setRating(item, star)"
                />
              </div>
            </li>
            <li>
              <span class="right">
                <PencilIcon
                  @click="editMovie(item)"
                  class="text-red-400 h-5 w-5"
                  style="cursor: pointer; display: inline"
                />&nbsp;
                <TrashIcon
                  @click="deleteMovie(item.id)"
                  class="text-gray-400 h-5 w-5"
                  style="cursor: pointer; display: inline"
                />
              </span>
            </li>
          </ul>
        </div>
      </template>
    </div>
    <span class="bottom-left">
      Total movies: {{ movies.length }} / Average rating: {{ (movies.reduce((total, movie) => total += movie.rating,
          0) / movies.length).toFixed(2) }}</span>
    <button
      @click="movies.map(movie => movie.rating = 0)"
      class="btn bottom-right-button"
      style="right: 10%;"
    >Remove ratings</button>
    <button
      @click="showForm = true"
      class="btn bottom-right-button"
    >New movie</button>
  </div>

  <dialog
    open
    v-show="showForm"
    class="form"
  >
    <form
      action=""
      class="nice-form-group"
      @submit.prevent="saveMovie()"
    >
      <label for="name">Name</label>
      <input
        id="name"
        type="text"
        placeholder="Name"
        required
        v-model="formValues.name"
      />
      <br />
      <label for="description">Description</label>
      <textarea
        id="description"
        type="text"
        placeholder="Description"
        v-model="formValues.description"
      ></textarea>
      <br />
      <label for="image">Image</label>
      <input
        id="image"
        type="text"
        placeholder="Image"
        v-model="formValues.image"
      />
      <br />
      <label for="genres">Genre</label>
      <select
        id="genres"
        placeholder="Genre"
        multiple
        required
        style="height: 7.3rem;"
        v-model="formValues.genres"
      >
        <option value="Action">Action</option>
        <option value="Adventure">Adventure</option>
        <option value="Comedy">Comedy</option>
        <option value="Crime">Crime</option>
        <option value="Drama">Drama</option>
        <option value="Mystery">Mystery</option>
        <option value="Science Fiction">Science Fiction</option>
        <option value="Terror">Terror</option>
      </select>
      <br />
      <label for="rating">Rating</label>
      <input
        id="rating"
        type="number"
        step="1"
        min="0"
        max="5"
        placeholder="Nota"
        v-model="formValues.rating"
      />
      <br />
      <input
        id="inTheaters"
        type="checkbox"
        value="false"
        style="margin-left: 5%;"
        v-model="formValues.inTheaters"
      />&nbsp;
      <label
        for="inTheaters"
        style="margin-left: 0; font-weight: 700;"
      >In theaters</label>
      <br />
      <button
        type="submit"
        class="btn right"
        style="margin-right: 5%; margin-bottom: 2%;"
      >Save</button>
      <button
        type="button"
        class="btn right"
        @click="showForm = false"
      >Cancel</button>
    </form>
  </dialog>

</template>
<style scoped>
h1 {
  font-weight: bolder;
  font-size: 1.5rem;
}

h2 {
  font-size: 1.2rem;
}

.container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;

}

.movie {
  margin: 10px;
  max-width: 340px;
  display: inline-flex;
  background-color: lightgray;
  flex-direction: column;
  align-content: space-around;
  flex-wrap: wrap;
}

.stars {
  padding-top: 3px;
  display: inline-block;
}

.poster {
  width: 400px;
  height: 500px;
  object-fit: cover;
}

.posterContainer {
  position: relative;
}

.top-right {
  position: absolute;
  top: 8px;
  right: 16px;
}

.genre {
  width: fit-content;
  padding-left: 5px;
  padding-right: 5px;
  margin-right: 5px;
  font-size: small;
  border-radius: 12px;
  background-color: #3a65b6;
  color: white;
}

.btn {
  background-color: #4CAF50;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
}

.right {
  float: right;
}

.bottom-right-button {
  position: absolute;
  bottom: 5px;
  right: 5px;
}

.bottom-left {
  position: absolute;
  bottom: 5px;
  left: 5px;
}

.form {
  position: fixed;
  top: 20%;
  left: 5%;
  width: 60%;
  background-image: linear-gradient(45deg,
      magenta,
      rebeccapurple,
      dodgerblue,
      green);
}
</style>