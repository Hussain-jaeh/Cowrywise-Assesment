<script setup>
import { ref } from 'vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { fas } from '@fortawesome/free-solid-svg-icons';

library.add(fas);

const props = defineProps({
  searchQuery: {
    type: String,
    default: ''
  },
  handleKeyPress: {
    type: Function,
    default: () => {}
  }
});

const emit = defineEmits(['search']);

const internalSearchQuery = ref(props.searchQuery);

const emitSearch = () => {
  emit('search', internalSearchQuery.value);
};

</script>

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
  font-weight: 500;
}

.search-icon {
  color: #2538589b;
  cursor: pointer;
}

.search-bar input {
  flex-grow: 1;
  border: none;
  outline: none;
  font-size: 1rem;
  margin-left: 0.5rem;
  color: #1e3e62;
}
</style>