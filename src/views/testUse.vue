<template>
  <div>{{ notifications }}</div>

  <h2>Notifications</h2>
  <ul>
    <li v-for="(notification, index) in notifications" :key="index">
      <div :class="{ read: notification.read, unread: !notification.read }">
        {{ notification.notificationId }} <span>{{ notification.message }}</span>
        <button v-if="!notification.read" @click="markAsRead(notification.notificationId)">
          Mark as Read
        </button>
      </div>
    </li>
  </ul>
</template>

<script>
import axios from "axios";
import { useAuthStore } from "@/stores/auth";
//import Swal from "sweetalert2";

const API_URL = "http://49.245.48.28:8080/api";

export default {
  name: "testPage",
 
  data() {
    return {
      notifications: [],
      New_notifications: [],
    };
  },

  async mounted() {
    const auth = useAuthStore();
    if (auth.isAuthenticated) {
      const headers = {
        "session-ID": auth.jsessionID,
      };

      axios
        .get(`${API_URL}/notification/retrieveAny?email=${auth.email}`, { headers })
        .then((response) => {
          console.log(response.data);
          this.notifications = response.data;
        })
        .catch((error) => {
          console.error(error);
        });
      axios
        .get(`${API_URL}/notification/retrieveNew?email=${auth.email}`, { headers })
        .then((response) => {
          console.log(response.data);
          this.New_notifications = response.data;
        })
        .catch((error) => {
          console.error(error);
        });
    }
  },
  methods: {
    async markAsRead(notification) {
      try {
        const auth = useAuthStore();
        if (auth.isAuthenticated) {
          axios
            .post(
              `${API_URL}/notification/markAsRead?notificationID=${notification.notificationId}`,
              {},

              {
                headers: {
                  "session-ID": auth.jsessionID != null ? auth.jsessionID : "Placeholder",
                },
              }
            )
            .then((response) => {
              console.log(response);
              notification.read = true;
            });
        }
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
