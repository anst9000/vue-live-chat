<template>
  <div class="chat-window">
    <div v-if="error">{{ error }}</div>
    <div v-if="formattedDocuments" ref="messages" class="messages">
      <div v-for="doc in formattedDocuments" :key="doc.id" class="single">
        <span class="created-at">{{ doc.createdAt }}</span>
        <span class="name">{{ doc.name }}</span>
        <span class="message">{{ doc.message }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import getCollection from '../composables/getCollection'
import { computed, onUpdated, ref } from 'vue'
import { formatDistanceToNow } from 'date-fns'
export default {
  setup() {
    const { error, documents } = getCollection('messages')
    // format timestamp
    const formattedDocuments = computed(() => {
      if (documents.value) {
        return documents.value.map(doc => {
          let time = formatDistanceToNow(doc.createdAt.toDate())
          return { ...doc, createdAt: time }
        })
      }
    })
    // auto-scroll to bottom of messages
    const messages = ref(null)
    onUpdated(() => {
      messages.value.scrollTop = messages.value.scrollHeight
    })
    return { error, documents, formattedDocuments, messages }
  }
}
</script>

<style scoped>
  .chat-window {
    background: #fafafa;
    padding: 1.5rem 1.5rem 1.0rem;
  }
  .single {
    margin: 12px 0;
    padding-bottom: 6px;
    border-bottom: 1px dotted #999;
  }
  .created-at {
    display: block;
    color: #999;
    font-size: 12px;
    margin-bottom: 4px;
  }
  .name {
    font-weight: bold;
    padding-right: 1rem;
    margin-right: 1rem;
    border-right: 1px solid #999;
  }
  .messages {
    max-height: 400px;
    overflow: auto;
  }
</style>