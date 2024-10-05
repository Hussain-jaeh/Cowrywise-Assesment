<script setup>
import { ref, onMounted, watch } from 'vue';
import axios from 'axios';
import SkeletonLoader from '../components/SkeletonLoader.vue';
import ModalComponent from '../components/ModalComponent.vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { fas } from '@fortawesome/free-solid-svg-icons';

library.add(fas);

const photos = ref([]);
const searchQuery = ref('');
const isModalOpen = ref(false);
const selectedPhoto = ref({});
const searchLoading = ref(false)
const searchResults = ref(false)
const loading = ref(false);
const noResults = ref(false);

  const handleSearch = () => {
  fetchPhotos(searchQuery.value).then(() => {
    searchResults.value = true
  })
}

const handleKeyPress = (event) => {
  if (event.key === 'Enter') {
    event.preventDefault()
   handleSearch()
  }
}

const unsplashAccessKey = import.meta.env.VITE_UNSPLASH_ACCESS_KEY;

const fetchPhotos = async (query) => {
  loading.value = true;
  noResults.value = false;

  try {
    const endpoint = 'https://api.unsplash.com/search/photos';
    const response = await axios.get(endpoint, {
      headers: {
        Authorization: `Client-ID ${unsplashAccessKey}`,
      },
      params: {
        query,
        per_page: 8,
      },
    });

    photos.value = response.data.results;

    
    noResults.value = photos.value.length === 0;
  } catch (error) {
    console.error('Error fetching photos:', error);
  } finally {
    loading.value = false;
  }
};

const openModal = (photo) => {
  selectedPhoto.value = photo;
  isModalOpen.value = true;
};

const closeModal = () => {
  isModalOpen.value = false;
};
onMounted(() => {
  fetchPhotos("africa");
});
</script>

<template>
  <header class="header">
    <div class="search-bar"  v-if="!searchLoading && !searchResults">
      <font-awesome-icon :icon="['fas', 'magnifying-glass']" class="search-icon" @click="handleKeyPress" />
      <input
        type="text"
         placeholder="Search for photos"
        v-model="searchQuery"
        @keypress="handleKeyPress"
       
      />
    </div>
     <div class="searchText" v-if="searchLoading">
        <h1>
          Searching for <span>"{{ searchQuery }}"</span>
        </h1>
      </div>
      <div class="searchTextFor" v-if="searchResults">
        <h1>
          Search Results for <span>"{{ searchQuery }}"</span>
        </h1>
      </div>

  </header>
  <main class="content">
    <div class="image-grid">
      <SkeletonLoader v-if="loading" v-for="n in 8" :key="n" />
          

      <div v-else v-for="(photo, index) in photos" :key="photo.id" class="image-grid-item" @click="openModal(photo)">
        <img :src="photo.urls.regular" :alt="photo.alt_description || 'Unsplash photo'" />
        <div class="overlay">
          <div class="text">
            <h4>{{ photo.user.name }}</h4>
            <p>{{ photo.user.location || 'Unknown Location' }}</p>
          </div>
        </div>
      </div>
    </div>
       <div v-if="noResults" class="no-results">
           <h2>Results not found</h2>
            </div>
    <ModalComponent :selectedCard="selectedPhoto" :showModal="isModalOpen" :closeModal="closeModal" />
  </main>
</template>




<style scoped>
.header {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #dde2e9;
  height: 30vh;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin: -1rem;
}

.search-bar {
  background: #fff;
  padding: 2rem 2rem;
  border-radius: 8px;
  width: 80%;
  margin: 0 auto;
  display: flex;
  align-items: center;
  gap: 24px;
}

.search-bar input::placeholder {
  color: #253858;
  font-weight: 700;
  font-size:1.2rem;
}

.search-icon {
  color: #2538589b;
  cursor: pointer;
    font-size:1.2rem;
}

.search-bar input {
  flex-grow: 1;
  border: none;
  outline: none;
  font-size: 1rem;
  margin-left: 0.5rem;
  color: #1e3e62;
}
.content {
  padding: 2rem;
  max-width: 1000px;
  margin: -3rem auto 0;
}

.image-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr); 
  gap: 2rem;
  justify-content: center;
}
@media (max-width: 1000px) {
  .image-grid {

  grid-template-columns: repeat(2, 1fr); 


}
  .image-grid-item:nth-child(4),
  .image-grid-item:nth-child(6) {
    transform: none !important;
  }

  .image-grid-item:nth-child(1):hover,
  .image-grid-item:nth-child(3):hover {
  transform: none !important;
  }

  
 }

@media (max-width: 768px) { 
  .image-grid {
     display: flex;
     align-items:center;
     justify-content:center;
    flex-direction: column; 
    gap: 2rem; 
  }
  

  .image-grid-item:nth-child(4),
  .image-grid-item:nth-child(6) {
    transform: none !important;
  }

  .image-grid-item:nth-child(1):hover,
  .image-grid-item:nth-child(3):hover {
  transform: none !important;
  }
    .image-grid-item {
    width: 90%; 
    height: auto; 
    max-width: 400px; 
  }

  .image-grid-item img {
    width: 100%;
    height: auto; 
  }
  .search-bar {

  padding:1.5rem;
  margin:2rem;

}
  .searchTextFor{
    margin:2rem;
  }
}

.image-grid-item {
  position: relative;
  width: 100%; 
  height: auto; 
  aspect-ratio: 3 / 4;
  overflow: hidden;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
}

.image-grid-item img {
  width: 100%;
  height: 100%;
  object-fit: cover; 
}

.image-grid-item:hover {
  transform: scale(1.05); 
}

.image-grid-item:nth-child(1),
.image-grid-item:nth-child(3) {
 height:70%;
}

.image-grid-item:nth-child(4),
.image-grid-item:nth-child(6) {
  transform: translateY(-100px);
}

.image-grid-item:nth-child(1):hover,
.image-grid-item:nth-child(3):hover {
  transform: translateY(-20px) scale(1.05);
}
.image-grid-item:nth-child(8):hover {
margin-top:-2rem;
}
.overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.7), transparent);
  color: white;
  padding: 1rem;
  text-align: left;
}

.text h4 {
  margin: 0;
  font-size: 1.2rem;
  font-weight: bold;
}

.text p {
  margin: 0.5rem 0 0;
  font-size: 1rem;
  opacity: 0.8;
}.modal {
  display: flex;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.8); 
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {

    position: relative;
    background-color: transparent;
    width: 700px;
    height: 100%;
    display: flex;
    margin: 0 auto;
    flex-direction: column;
    justify-content: center;
    text-align: center;
}

.modal img {
  width: 100%; 
  height: 70%; 
  border-radius: 8px; 
}

.modal-info {
        border-radius: 8px; 
       height: 15%;
        width: 100%;
        background: white;
        display: flex;
        flex-direction: column;
        justify-content: center;
        padding: 0 2rem;
        align-items:flex-start;
}
.modal-info h4{
    font-weight: 600;
   font-size: 1.5rem;
    color: #253858;
  letter-spacing: -0.5px;
  margin-bottom:0.4rem;
}
.modal-info p{
  font-weight: 600;
  font-size: 90%;
   color: #6d7b91;
  letter-spacing: -0.5px;
   font-size: 1.2rem;
}

.close {
  position: fixed;
    top: 2rem;
    right: 16rem;
    font-size: 2rem;
    color:white;
  cursor: pointer;
}
.no-results {
  display: flex;
  justify-content: center; 
  align-items: center;    
  height: 100%;            
  text-align: center;
  font-size: 1.5rem;
  color: #6d7b91;
  margin-top:9rem;
}

</style>
