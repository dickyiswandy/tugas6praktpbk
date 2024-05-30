<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-toolbar-title>
          Project Dicky
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <q-page padding>
        <q-card class="q-pa-md q-mb-md">
          <q-form @submit.prevent="save">
            <q-input
              filled
              v-model="form.title"
              label="Title"
              class="q-mb-md"
              lazy-rules
              :rules="[val => !!val || 'Title is required']"
            />
            <q-input
              filled
              v-model="form.content"
              label="Content"
              type="textarea"
              class="q-mb-md"
              lazy-rules
              :rules="[val => !!val || 'Content is required']"
            />
            <q-btn type="submit" label="Save" color="primary" class="q-mr-md" />
            <q-btn type="reset" label="Reset" color="warning" @click="resetForm" />
          </q-form>
        </q-card>
        <q-card class="q-pa-md">
          <q-list bordered separator>
            <q-item v-for="article in articles" :key="article.id" clickable>
              <q-item-section>
                <q-item-label>{{ article.title }}</q-item-label>
                <q-item-label caption>{{ article.content }}</q-item-label>
              </q-item-section>
              <q-item-section side>
                <q-btn icon="edit" color="primary" @click="edit(article)" class="q-mr-sm" />
                <q-btn icon="delete" color="negative" @click="remove(article.id)" />
              </q-item-section>
            </q-item>
          </q-list>
        </q-card>
        <q-btn @click="load" label="Load Articles" color="secondary" class="q-mt-md" />
      </q-page>
    </q-page-container>

    <q-footer class="bg-grey-8 text-white q-pa-md">
      <div>&copy; 2024 Integrasi Dengan API. All rights reserved.</div>
      <div>Contact Person: dickyiswandy@student.uir.ac.id</div>
    </q-footer>
  </q-layout>
</template>

<script setup>
import { reactive, ref, onMounted } from 'vue';
import axios from 'axios';
import { QLayout, QHeader, QPageContainer, QFooter, QPage, QCard, QForm, QInput, QBtn, QList, QItem, QItemSection, QItemLabel } from 'quasar';

// Define form data
const form = reactive({
  id: null,
  title: '',
  content: '',
});

// Articles list
const articles = ref([]);

// Load articles from server
async function load() {
  try {
    const response = await axios.get('http://localhost:3000/articles');
    articles.value = response.data;
    console.log('Articles loaded:', articles.value);
  } catch (error) {
    console.error('Error loading articles', error);
  }
}

// Save or update article
async function save() {
  try {
    if (form.id) {
      await axios.put(`http://localhost:3000/articles/${form.id}`, {
        title: form.title,
        content: form.content,
      });
    } else {
      await axios.post('http://localhost:3000/articles', {
        title: form.title,
        content: form.content,
      });
    }
    load();
    resetForm();
  } catch (error) {
    console.error('Error saving article', error);
  }
}

// Edit article
function edit(article) {
  form.id = article.id;
  form.title = article.title;
  form.content = article.content;
}

// Delete article
async function remove(id) {
  try {
    await axios.delete(`http://localhost:3000/articles/${id}`);
    load();
  } catch (error) {
    console.error('Error deleting article', error);
  }
}

// Reset form
function resetForm() {
  form.id = null;
  form.title = '';
  form.content = '';
}

// Load articles on component mount
onMounted(load);
</script>

<style>
.q-page {
  max-width: 800px;
  margin: 0 auto;
}

.q-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
