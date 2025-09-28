<template>
  <template v-if="error">
    <ErrorCard :retry="fetchEvents">
      Could not load events at the moment. Please try again.
    </ErrorCard>
  </template>
  <template v-else>
    <section class="grid grid-cols-1 lg:grid-cols-2 gap-8">
      <template v-if="!loading">
        <template v-if="events.length">
          <EventCard
            v-for="event in events"
            :title="event.title"
            :when="event.date"
            :description="event.description"
            :key="event.id"
            @register="handleRegistration(event)"
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
import EventCard from '@/components/EventCard.vue';
import LoadingEventCard from '@/components/LoadingEventCard.vue';
import useBookings from '@/composables/useBookings';
import ErrorCard from '@/components/ErrorCard.vue';

const { handleRegistration, fetchEvents, loading, error, events } = useBookings();

onMounted(() => fetchEvents());
</script>
