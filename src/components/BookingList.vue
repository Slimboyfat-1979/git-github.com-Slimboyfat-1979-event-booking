<template>
  <template v-if="error">
    <ErrorCard :retry="fetchBookings">Failed to fetch bookings</ErrorCard>
  </template>
  <template v-else>
    <section class="grid grid-cols-1 gap-8">
      <div v-if="!loading">
        <BookingItem
          v-for="booking in bookings"
          :key="booking.id"
          :title="booking.eventTitle"
          :status="booking.status"
          @cancelled="cancelBooking(booking.id)"
        ></BookingItem>
      </div>
    </section>
  </template>
</template>

<script setup>
import { onMounted } from 'vue';
import BookingItem from '@/components/BookingItem.vue';
import LoadingBookingCard from '@/components/LoadingBookingCard.vue';
import useBookings from '@/composables/useBookings';
import ErrorCard from '@/components/ErrorCard.vue';
import EventCard from './EventCard.vue';

const { bookings, loading, fetchBookings, error, cancelBooking } = useBookings();

onMounted(() => {
  fetchBookings();
});
</script>

<!-- <template v-if="error">
    <ErrorCard :retry="fetchBookings">Failed to fetch bookings</ErrorCard>
  </template>
  <template v-else>
     <section class="grid grid-cols-1 gap-8">
      <template v-if="!loading">
        <BookingItem
          v-for="booking in bookings"
          :key="booking.id"
          :title="booking.eventTitle"
          :status="booking.status"
          @cancelled="cancelBooking(booking.id)"
        ></BookingItem>
      </template>
      <template v-else>
        <LoadingBookingCard v-for="i in 4" :key="i"></LoadingBookingCard>
      </template>
    </section>
  </template> -->
