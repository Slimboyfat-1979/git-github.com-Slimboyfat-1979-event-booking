<template>
  <main class="container mx-auto my-8 space-y-8">
    <h1 class="text-4xl">Event Booking App</h1>
    <h2 class="text-2xl font-medium">All Events</h2>
    <section class="grid grid-cols-2 gap-8">
      <EventCard @register="register" title="vue conference 2025" when="2025-05-01" description="Conference about Vue and Javascript" v-for="i in 8" :key="i" />
    </section>
    <h2 class="text-2xl font-medium">Your Bookings</h2>
  </main>
</template>

<script setup>
import EventCard from '@/components/EventCard.vue';



</script>
