<template>
  <div id="app">
    <div class="container">
      <div>
        <input type="text" v-model="newArticle.title" placeholder="Judul artikel">
        <textarea v-model="newArticle.content" placeholder="Isi artikel"></textarea>
        <button @click="saveData">Simpan</button>
      </div>
      <button @click="loadData">Load Data</button>
      <div v-if="dataLoaded" class="article-list">
        <div v-if="loading" class="loader"></div>
        <div v-else class="article-container">
          <div v-for="article in articles" :key="article.id" class="article-item">
            <h3>{{ article.title }}</h3>
            <p>{{ article.content }}</p>
            <div class="article-buttons">
              <button @click="editArticle(article)">Edit</button>
              <button @click="deleteArticle(article.id)">Hapus</button>
            </div>
          </div>
        </div>
      </div>
      <div v-if="editMode">
        <input type="text" v-model="editedArticle.title" placeholder="Judul artikel">
        <textarea v-model="editedArticle.content" placeholder="Isi artikel"></textarea>
        <button @click="updateArticle">Update</button>
        <button @click="cancelEdit">Batal</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      articles: [],
      dataLoaded: false,
      loading: false,
      newArticle: {
        title: '',
        content: ''
      },
      editedArticle: {
        id: '',
        title: '',
        content: ''
      },
      editMode: false
    };
  },
  methods: {
    loadData() {
      this.loading = true;
      axios.get('http://localhost:3000/articles')
        .then(response => {
          this.articles = response.data;
          this.dataLoaded = true;
          this.loading = false;
        })
        .catch(error => {
          console.error("There was an error loading the data!", error);
          this.loading = false;
        });
    },
    saveData() {
      axios.post('http://localhost:3000/articles', this.newArticle)
        .then(response => {
          console.log("Data saved successfully!", response.data);
          this.newArticle.title = '';
          this.newArticle.content = '';
          this.loadData(); // Reload data after saving
        })
        .catch(error => {
          console.error("There was an error saving the data!", error);
        });
    },
    deleteArticle(articleId) {
      axios.delete(`http://localhost:3000/articles/${articleId}`)
        .then(response => {
          console.log("Article deleted successfully!", response.data);
          this.loadData(); // Reload data after deleting
        })
        .catch(error => {
          console.error("There was an error deleting the article!", error);
        });
    },
    editArticle(article) {
      this.editMode = true;
      this.editedArticle.id = article.id;
      this.editedArticle.title = article.title;
      this.editedArticle.content = article.content;
    },
    updateArticle() {
      axios.put(`http://localhost:3000/articles/${this.editedArticle.id}`, this.editedArticle)
        .then(response => {
          console.log("Article updated successfully!", response.data);
          this.editMode = false;
          this.editedArticle.id = '';
          this.editedArticle.title = '';
          this.editedArticle.content = '';
          this.loadData(); // Reload data after updating
        })
        .catch(error => {
          console.error("There was an error updating the article!", error);
        });
    },
    cancelEdit() {
      this.editMode = false;
      this.editedArticle.id = '';
      this.editedArticle.title = '';
      this.editedArticle.content = '';
    }
  }
};
</script>

<style>
body {
  font-family: 'Roboto', sans-serif;
  background-color: #87ceeb; /* Background biru pastel */
  margin: 0;
  padding: 0;
  color: #333333; /* Warna teks hitam */
}

.container {
  max-width: 800px;
  margin: 20px auto;
  padding: 20px;
  background-color: #ffc0cb; /* Kontainer berwarna pink pastel */
  border-radius: 10px; /* Sudut yang sedikit bulat */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Bayangan lembut */
}

.container button {
  margin-bottom: 12px; /* Jarak antara tombol dengan elemen lain */
}

h2 {
  font-size: 24px;
  font-weight: bold;
  color: #333333; /* Warna teks hitam */
  margin: 0;
}

p {
  font-size: 16px;
  color: #666666; /* Warna teks abu-abu */
  margin: 0;
}

input[type="text"],
textarea {
  width: calc(100% - 22px);
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #cccccc; /* Border yang lebih ringan */
  border-radius: 8px; /* Input yang lebih bulat */
  background-color: #ffffff; /* Latar belakang putih */
  color: #333333; /* Warna teks hitam */
}

button {
  padding: 7px 12px;
  background-color: #87ceeb; /* Warna tombol biru pastel */
  color: #ffffff; /* Warna teks putih */
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-right: 8px;
}

.article-buttons button {
  margin-left: 0;
  margin-bottom: 8px;
}

.article-buttons button + button {
  margin-left: 8px;
}

.loader {
  border: 6px solid #f3f3f3;
  border-top: 6px solid #87ceeb; /* Warna biru pastel */
  border-radius: 50%;
  width: 50px;
  height: 50px;
  animation: spin 1s linear infinite;
  margin: 20px auto;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.article-container {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.article-item {
  background-color: #f0f8ff; /* Latar belakang biru putih */
  padding: 16px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* Bayangan lembut */
}

.article-item h3 {
  margin: 0 0 8px 0;
  color: #333333; /* Warna teks hitam */
}

.article-item p {
  margin: 0 0 12px 0;
  color: #666666; /* Warna teks abu-abu */
}

.article-buttons {
  display: flex;
  gap: 8px;
}

@media screen and (max-width: 768px) {
  .container {
    padding: 10px;
  }

  h2 {
    font-size: 20px;
  }

  p {
    font-size: 14px;
  }

  input[type="text"],
  textarea,
  button {
    width: calc(100% - 22px);
  }
}
</style>
