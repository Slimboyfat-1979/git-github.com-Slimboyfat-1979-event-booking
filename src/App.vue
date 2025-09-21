<template>
  <main class="container mx-auto my-8 space-y-8">
    <h1 class="text-4xl">Event Booking App</h1>
    <h2 class="text-2xl font-medium">All Events</h2>
    <section class="grid grid-cols-2 gap-8">
      <template v-if="!eventsLoading">
        <EventCard
          v-for="event in events"
          :title="event.title"
          :when="event.date"
          :description="event.description"
          :key="event.id"
          @register="handleReginstration(event)"
        />
      </template>
      <template v-else>
        <LoadingEventCard v-for="i in 4" :key="i"></LoadingEventCard>
      </template>
    </section>
    <h2 class="text-2xl font-medium">Your Bookings</h2>
    <section class="grid grid-cols-1 gap-8">
      <template v-if="!bookingsLoading">
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
  </main>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import EventCard from '@/components/EventCard.vue';
import BookingItem from './components/BookingItem.vue';
import LoadingEventCard from './components/LoadingEventCard.vue';
import LoadingBookingCard from './components/LoadingBookingCard.vue';

const events = ref([]);
const bookings = ref([]);
const eventsLoading = ref(false);
const bookingsLoading = ref(false);

const fetchEvents = async () => {
  eventsLoading.value = true;
  try {
    const response = await fetch('http://localhost:3001/events');
    events.value = await response.json();
  } finally {
    eventsLoading.value = false;
  }
};

const fetchBookings = async () => {
  try {
    bookingsLoading.value = true;
    const response = await fetch('http://localhost:3001/bookings');
    bookings.value = await response.json();
  } finally {
    bookingsLoading.value = false;
  }
};

const findBookingbyId = (id) => {
    bookings.value.findIndex((b) => b.id === id)
}

const handleReginstration = async (event) => {
 if(bookings.value.some((booking) => booking.eventId === event.id && booking.userId === 1)) {
    alert('You are already registered for this event');
    return;
 }

  const newBooking = {
    id: Date.now().toString(),
    userId: 1,
    eventId: event.id,
    eventTitle: event.title,
    status: 'pending',
  };

  bookings.value.push(newBooking);

  try {
      const response = await fetch('http://localhost:3001/bookings', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          ...newBooking,
          status: 'confirmed',
        }),
      });

      if(response.ok) {  
        const index = findBookingbyId(newBooking.id);
        bookings.value[index] = await response.json();
      }else{
        throw new Error('Failed to confirm booking');
      }
  } catch (e) {
    console.error(`Failed to register for event: `, e);
    bookings.value = bookings.value.filter(b => b.id != newBooking.id)
  }
};


const cancelBooking = async (bookingId) => {
    const index = findBookingbyId(bookingId);
    const originalBooking = bookings.value[index];
    bookings.value.splice(index, 1);

    try{
        const response = await fetch('http://localhost:3001/bookings/' + bookingId, {
            method: 'DELETE'
        });
        if(!response.ok) {
            throw new Error('Booking could not be cancelled')
        }
    }catch(error) {
        console.error('Failed to cancel booking', error);
        bookings.value.splice(index, 0, originalBooking)
    }
}


onMounted(function () {
  fetchEvents();
  fetchBookings();
});
</script>
