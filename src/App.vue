<template>
  <div id="app">
    <h1 class="logo">Hallo <span class="highlight">People</span></h1>
    <div class="container">
      <div>
        <label for="new-title" class="input-label">Judul Artikel:</label>
        <input id="new-title" type="text" v-model="newArticle.title" placeholder="Judul artikel">
        <label for="new-content" class="input-label">Isi Artikel:</label>
        <textarea id="new-content" v-model="newArticle.content" placeholder="Isi artikel"></textarea>
        <button @click="saveData" class="button save-button">
          <i class="fas fa-save"></i> Simpan
        </button>
      </div>
      <button @click="loadData" class="button">
        <i class="fas fa-sync"></i> Load Data
      </button>
      <div v-if="dataLoaded" class="article-list">
        <div v-if="loading" class="loader"></div>
        <div v-else>
          <div v-for="article in articles" :key="article.id" class="article-item">
            <h3>{{ article.title }}</h3>
            <p>{{ article.content }}</p>
            <div class="article-buttons">
              <button @click="editArticle(article)" class="button">
                <i class="fas fa-edit"></i> Edit
              </button>
              <button @click="deleteArticle(article.id)" class="button">
                <i class="fas fa-trash-alt"></i> Hapus
              </button>
            </div>
          </div>
        </div>
      </div>
      <div v-if="editMode" class="edit-form">
        <label for="edit-title" class="input-label">Judul artikel:</label>
        <input id="edit-title" type="text" v-model="editedArticle.title" placeholder="Judul artikel">
        <label for="edit-content" class="input-label">Isi artikel:</label>
        <textarea id="edit-content" v-model="editedArticle.content" placeholder="Isi artikel"></textarea>
        <button @click="updateArticle" class="button">
          <i class="fas fa-check"></i> Update
        </button>
        <button @click="cancelEdit" class="button">
          <i class="fas fa-times"></i> Batal
        </button>
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
          this.loadData();
        })
        .catch(error => {
          console.error("There was an error saving the data!", error);
        });
    },
    deleteArticle(articleId) {
      axios.delete(`http://localhost:3000/articles/${articleId}`)
        .then(response => {
          console.log("Article deleted successfully!", response.data);
          this.loadData();
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
          this.loadData();
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
  background-color: #1c1c1c;
  margin: 0;
  padding: 0;
  color: #3498db;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.logo {
  font-size: 48px;
  font-weight: bold;
  color: #ffffff;
  margin: 20px 0;
}

.highlight {
  background-color: #3498db;
  color: #000000;
  padding: 0 10px;
  border-radius: 4px;
}

.container {
  max-width: 800px;
  margin: 20px auto;
  padding: 20px;
  background-color: #2c2c2c;
  border-radius: 10px;
  box-shadow: 0 4px 15px rgba(52, 152, 219, 0.5);
  color: #3498db;
}

.container button {
  margin-bottom: 12px;
}

.input-label {
  display: block;
  margin-bottom: 5px;
  color: #3498db;
  font-weight: bold;
}

h3 {
  font-size: 24px;
  font-weight: bold;
  color: #3498db;
  margin: 0;
}

p {
  font-size: 16px;
  color: #3498db;
  margin: 0;
}

input[type="text"],
textarea {
  width: calc(100% - 22px);
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #444;
  border-radius: 8px;
  background-color: #3c3c3c;
  color: #3498db;
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
}

input[type="text"]:focus,
textarea:focus {
  border-color: #2980b9;
  box-shadow: 0 0 5px rgba(41, 128, 185, 0.5);
  outline: none;
}

button {
  padding: 7px 12px;
  background-color: #3498db;
  color: #000;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.3s ease, box-shadow 0.3s ease;
  margin-right: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
}

button:hover {
  background-color: #2980b9;
  transform: scale(1.05);
  box-shadow: 0 6px 8px rgba(0, 0, 0, 0.3);
}

button:active {
  background-color: #1c6ea4;
  transform: scale(0.95);
}

.article-buttons button {
  margin-left: 0;
  margin-bottom: 8px;
}

.article-buttons button+button {
  margin-left: 8px;
}

.loader {
  border: 6px solid #f3f3f3;
  border-top: 6px solid #3498db;
  border-radius: 50%;
  width: 50px;
  height: 50px;
  animation: spin 1s linear infinite;
  margin: 20px auto;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

.article-list {
  background-color: #1c1c1c;
  padding: 10px;
  border-radius: 10px;
}

.article-item {
  border-bottom: 1px solid #3498db;
  padding: 10px;
}

.article-item h3 {
  margin: 0 0 10px 0;
  color: #3498db;
}

.article-item p {
  margin: 0;
  color: #3498db;
}

.article-item .article-buttons {
  margin-top: 10px;
}

.edit-form {
  margin-top: 20px;
}

@media screen and (max-width: 768px) {
  .container {
    padding: 10px;
  }

  h3 {
    font-size: 20px;
  }

  p {
    font-size: 14px;
  }
}
</style>
