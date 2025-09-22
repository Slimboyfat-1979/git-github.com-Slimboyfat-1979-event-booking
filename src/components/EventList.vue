<template>
  <section class="grid grid-cols-2 gap-8">
    <template v-if="!loading">
      <EventCard
        v-for="event in events"
        :title="event.title"
        :when="event.date"
        :description="event.description"
        :key="event.id"
        @register="$emit('register', event)"
      />
    </template>
    <template v-else>
      <LoadingEventCard v-for="i in 4" :key="i"></LoadingEventCard>
    </template>
  </section>
</template>

<script setup>
import {ref, onMounted} from 'vue';
import EventCard from './EventCard.vue';
import LoadingEventCard from './LoadingEventCard.vue';

defineEmits(['register']);

const events = ref([]);
const loading = ref(false);

const fetchEvents = async () => {
  loading.value = true;
  try {
    const response = await fetch('http://localhost:3001/events');
    events.value = await response.json();
  } finally {
    loading.value = false;
  }
};

onMounted(() => fetchEvents())
</script>
