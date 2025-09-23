<template>
  <template v-if="error">
    <SectionCard>
      <div class="space-y-4 items-center flex flex-col">
        <div class="text-red-500">Could not load events at the moment. Please try again.</div>
        <RoundButton @click="fetchEvents">Retry now</RoundButton>
      </div>
    </SectionCard>
  </template>
  <template v-else>
    <section class="grid grid-cols-2 gap-8">
      <template v-if="!loading">
        <template v-if="events.length">
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
          <div class="col-span-2 text-center text-gray-500">No Events Yet</div>
        </template>
      </template>
      <template v-else>
        <LoadingEventCard v-for="i in 4" :key="i"></LoadingEventCard>
      </template>
    </section>
  </template>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import EventCard from './EventCard.vue';
import LoadingEventCard from './LoadingEventCard.vue';
import SectionCard from '@/components/SectionCard.vue';
import RoundButton from '@/components/RoundButton.vue';

defineEmits(['register']);

const events = ref([]);
const loading = ref(false);
const error = ref(null);

const fetchEvents = async () => {
  loading.value = true;
  error.value = null;
  try {
    const response = await fetch('http://localhost:3001/events');
    events.value = await response.json();
  } catch (e) {
    error.value = e;
  } finally {
    loading.value = false;
  }
};

onMounted(() => fetchEvents());
</script>
