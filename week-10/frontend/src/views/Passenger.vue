<script>
import { mapActions } from 'vuex'
export default {
  name: 'PassengerView',
  data () {
    return {
      passenger: {},
      isLoading: true,
      drivers: [],
      destination: 'Tekirdağ'
    }
  },
  async mounted () {
    await this.updatePassenger()
    this.drivers = await this.fetchDrivers()
    this.isLoading = false
  },
  methods: {
    ...mapActions(['fetchPassenger', 'fetchDrivers', 'bookDriver']),
    async bookDriverAndUpdatePassenger ({ driverId, passengerId, origin, destination }) {
      await this.bookDriver({ driverId, passengerId, origin, destination })
      this.updatePassenger()
    },
    async updatePassenger () {
      this.passenger = await this.fetchPassenger(this.$route.params.passengerId)
    }
  }
}
</script>

<template lang="pug">
  .passenger
    p(v-if="isLoading") Please wait..
    div(v-else)
      h1 Passenger Detail
      p {{ passenger.name }}

      h2 Booking History
      div(v-if="passenger.bookings.length")
        ol
          li(v-for="booking in passenger.bookings")
            | From {{ booking.origin }} to {{ booking.destination }} with {{ booking.driver.name }}
      p(v-else) No bookings yet.
      h2 Create new booking
      p Destination
      input(v-model="destination")
      h3 Drivers
      ol
        li(v-for="driver in drivers")
          | From {{ driver.name }} is waiting at {{ driver.location }}
          button.book(@click="bookDriverAndUpdatePassenger({ driverId: driver._id, passengerId: passenger._id, origin: passenger.location, destination: destination })") Book Driver
</template>
