<template>
  <div>
    <h1
      class="text-3xl font-semibold text-teal-500 lg:text-4xl dark:text-white text-center py-5"
    >
      View journeys
    </h1>
    <section class="p-3 sm:p-5 mb-7">
      <LoginPrompt v-if="!isLoggedIn"></LoginPrompt>
      <div v-else class="mx-auto max-w-screen-xl px-4 lg:px-12">
        <div class="bg-white relative sm:rounded-lg overflow-hidden">
          <div
            class="flex flex-col md:flex-row items-center justify-between space-y-3 md:space-y-0 md:space-x-4 py-4"
          >
            <div class="w-full md:w-1/2">
              <form class="flex items-center">
                <label for="simple-search" class="sr-only">Search</label>
                <div class="relative w-full ml-1">
                  <div
                    class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none"
                  >
                    <svg
                      aria-hidden="true"
                      class="w-5 h-5 text-gray-500 dark:text-gray-400"
                      fill="currentColor"
                      viewbox="0 0 20 20"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <path
                        fill-rule="evenodd"
                        d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z"
                        clip-rule="evenodd"
                      />
                    </svg>
                  </div>
                  <input
                    v-model="searchQuery"
                    type="text"
                    id="simple-search"
                    class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-teal-500 focus:border-teal-500 block w-full pl-10 p-2 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-teal-500 dark:focus:border-teal-500"
                    placeholder="Search"
                    required=""
                  />
                </div>
              </form>
            </div>
            <div
              class="w-full md:w-auto flex flex-col md:flex-row space-y-2 md:space-y-0 items-stretch md:items-center justify-end md:space-x-3 flex-shrink-0"
            >
              <button
                type="button"
                class="flex items-center justify-center text-white bg-teal-700 hover:bg-teal-800 focus:ring-4 focus:ring-teal-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-teal-600 dark:hover:bg-teal-700 focus:outline-none dark:focus:ring-primary-800"
              >
                <Icon name="ep:plus" class="mr-2"></Icon>
                <NuxtLink
                  link="/"
                  isactive="false"
                  component="a"
                  linkattr="href"
                  to="/rides/new"
                  >Create a ride</NuxtLink
                >
              </button>
            </div>
          </div>
          <div class="overflow-x-auto">
            <table
              class="w-full text-sm text-left text-gray-500 dark:text-gray-400"
            >
              <thead
                class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400"
              >
                <tr>
                  <th scope="col" class="px-4 py-3">Destination</th>
                  <th scope="col" class="px-4 py-3">Start Point</th>
                  <th scope="col" class="px-4 py-3">Date</th>
                  <th scope="col" class="px-4 py-3">Driver</th>
                  <th scope="col" class="px-4 py-3">Spaces Available</th>
                  <th scope="col" class="px-4 py-3"></th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="(ride, index) in filteredRides"
                  :key="index"
                  class="border-b dark:border-gray-700"
                >
                  <th
                    scope="row"
                    class="px-4 py-3 font-medium text-gray-900 whitespace-nowrap dark:text-white"
                  >
                    {{ ride.toLocation }}
                  </th>
                  <td class="px-4 py-3">{{ ride.fromLocation }}</td>
                  <td class="px-4 py-3">{{ ride.dateOfDeparture }}</td>
                  <td class="px-4 py-3">{{ ride.driverId }}</td>
                  <td class="px-4 py-3">
                    {{ ride.spacesLeft === 0 ? "Full" : ride.spacesLeft }}
                  </td>
                  <td class="px-4 py-3">
                    <span
                      class="text-teal-600 hover:text-purple-600 cursor-pointer"
                      @click="openRideDetail(ride.id)"
                      >View</span
                    >
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </section>
    <PopUp :is-open="popupOpen" @close="popupOpen = false">
      <template #header>
        <h2
          class="text-2xl font-semibold text-teal-500 capitalize dark:text-white"
        >
          Journey detail: {{ selectedRide.fromLocation }} to
          {{ selectedRide.toLocation }}
        </h2>
      </template>
      <template #body>
        <RideDetails :ride="selectedRide"></RideDetails>
      </template>
      <template #footer>
        <div class="flex justify-end">
          <p
            v-if="!selectedRide.spacesLeft"
            class="text-gray-500 bg-gray-50 p-3 px-7 rounded-lg"
          >
            No spaces left
          </p>
          <p
            v-else-if="bookingMadePreviously"
            class="text-purple-500 bg-purple-50 p-3 px-5 rounded-lg"
          >
            Booked!
          </p>
          <button
            v-else-if="!currentUserIsDriver(selectedRide.driverId)"
            @click="requestBooking"
            type="button"
            class="text-white bg-teal-700 hover:bg-teal-800 focus:ring-4 focus:outline-none focus:ring-teal-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-teal-600 dark:hover:bg-teal-700 dark:focus:ring-teal-800"
          >
            Send a request to join
          </button>
        </div>
      </template>
    </PopUp>
  </div>
</template>
<script>
import PopUp from "~~/components/PopUp.vue";
import RideDetails from "../../components/RideDetails.vue";
import LoginPrompt from "~~/components/LoginPrompt.vue";
import { useMainStore } from "~~/MainStore";
export default {
  components: {
    PopUp,
    RideDetails,
    LoginPrompt,
  },
  setup() {
    const store = useMainStore();
    const popupOpen = ref(false);
    const searchQuery = ref("");
    const rides = ref([]);
    const selectedRide = ref({});
    const bookingMadePreviously = computed(() => {
      return (
        store.bookingsRequested.length > 0 &&
        store.bookingsRequested.includes(selectedRide.value.id)
      );
    });

    function currentUserIsDriver(driverId) {
      return driverId === store.userId;
    }

    const loginToken = useCookie("loginToken");

    async function getRides() {
      await fetch("http://localhost:9091/rideapi/rides", {
        headers: {
          Authorization: `Bearer ${loginToken.value}`,
        },
      })
        .then((response) => response.json())
        .then((data) => {
          rides.value = data;
        });
    }

    getRides();

    const filteredRides = computed(() => {
      const ridesWithSpaces = rides.value.filter(
        (ride) => ride.spacesLeft > 0 && !checkIsJourneyIsInThePast(ride)
      );
      if (searchQuery.value.length < 3) return ridesWithSpaces;
      return ridesWithSpaces.filter((ride) => {
        return (
          ride.fromLocation.includes(searchQuery.value) ||
          ride.toLocation.includes(searchQuery.value)
        );
      });
    });

    function checkIsJourneyIsInThePast(ride) {
      const journeyDateTime = new Date(
        `${ride.dateOfDeparture} ${ride.estimatedDepartureTime}`
      );
      const now = new Date();
      return journeyDateTime < now;
    }

    async function getRide(id) {
      await fetch(`http://localhost:9091/rideapi/rides/${id}`, {
        headers: {
          Authorization: `Bearer ${loginToken.value}`,
        },
      })
        .then((response) => response.json())
        .then((data) => {
          selectedRide.value = { ...data };
        });
    }

    function openRideDetail(id) {
      getRide(id);
      popupOpen.value = true;
    }

    async function requestBooking() {
      const bookingData = {
        rideId: selectedRide.value.id,
        passengerId: store.userId,
      };

      await fetch("http://localhost:9091/rideapi/rides/booking", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${loginToken.value}`,
        },
        body: JSON.stringify(bookingData),
      })
        .then((response) => response.json())
        .then((data) => {
          if (data.responseCode === "SUCCESS") {
            store.bookingsRequested.push(selectedRide.value.id);
          } else {
            console.log("booking failed");
          }
        });
    }

    return {
      selectedRide,
      getRide,
      searchQuery,
      filteredRides,
      popupOpen,
      openRideDetail,
      requestBooking,
      currentUserIsDriver,
      bookingMadePreviously,
      isLoggedIn: computed(() => store.isLoggedIn),
      bookingsRequested: computed(() => store.bookingsRequested),
    };
  },
};
</script>
