<template>
  <div id="app">
    <nav class="bg-gray-800 p-3 text-white">
      <div class="container flex items-center justify-between flex-wrap bg-gray-800">
        <div class="mr-6">
          <router-link
            class="font-semibold flex items-center flex-shrink-0 text-xl tracking-tight"
            to="/"
          >
            <img class="w-10 mr-2" src="/img/agola-logo-circle.svg" />
            Agola
          </router-link>
        </div>
        <div class="block lg:hidden">
          <button
            class="flex items-center px-3 py-2 border rounded text-blue-200 border-blue-400 hover:text-white hover:border-white"
            @click="navActive = !navActive"
          >
            <svg
              class="fill-current h-3 w-3"
              viewBox="0 0 20 20"
              xmlns="http://www.w3.org/2000/svg"
            >
              <title>Menu</title>
              <path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z" />
            </svg>
          </button>
        </div>
        <div
          class="w-full block flex-grow lg:flex lg:items-center lg:w-auto"
          :class="{'hidden' : !navActive}"
        >
          <div class="text-sm lg:flex-grow"></div>
          <div v-if="user" class="relative mr-3">
            <button
              v-click-outside="() => createDropdownActive = false"
              @click="createDropdownActive = !createDropdownActive"
              class="relative flex items-center focus:outline-none"
            >
              <i class="mdi mdi-plus-box mdi-24px" />
              <i class="mdi mdi-chevron-down"></i>
            </button>
            <div
              v-if="createDropdownActive"
              class="z-10 origin-top-right absolute right-0 mt-2 w-64 bg-white rounded-lg border shadow-md py-2 text-dark"
            >
              <ul>
                <li>
                  <router-link
                    class="block px-4 py-2 hover:bg-blue-500 hover:text-white"
                    to="/neworganization"
                  >New Organization</router-link>
                </li>
              </ul>
            </div>
          </div>
          <div v-if="user" class="relative">
            <div class="flex">
              <button
                v-click-outside="() => userDropdownActive = false"
                @click="userDropdownActive = !userDropdownActive"
                class="relative flex items-center focus:outline-none"
              >
                {{user.username}}
                <i class="mdi mdi-chevron-down"></i>
              </button>
            </div>
            <div
              v-if="userDropdownActive"
              class="z-10 origin-top-right absolute right-0 mt-2 w-64 bg-white rounded-lg border shadow-md py-2 text-dark"
            >
              <ul>
                <li class="block px-4 py-2 border-b">
                  Logged as&nbsp;
                  <b>{{user.username}}</b>
                </li>
                <li>
                  <hr class="navbar-divider" />
                </li>
                <li>
                  <router-link
                    class="block px-4 py-2 hover:bg-blue-500 hover:text-white"
                    :to="ownerSettingsLink('user', user.username)"
                  >
                    <i class="mr-1 mdi mdi-settings" />
                    <span>User Settings</span>
                  </router-link>
                </li>
                <li>
                  <router-link
                    class="block px-4 py-2 hover:bg-blue-500 hover:text-white"
                    to="/logout"
                  >
                    <i class="mdi mdi-logout"></i>Logout
                  </router-link>
                </li>
              </ul>
            </div>
          </div>
          <div v-else class="navbar-item">
            <router-link class="btn btn-blue" to="/register">Sign up</router-link>
            <router-link class="ml-2 btn btn-blue" to="/login">Login</router-link>
          </div>
        </div>
      </div>
    </nav>

    <div v-if="error" class="container h-screen" role="alert">
      <div v-if="error" class="h-full flex justify-center items-center" role="alert">
        <div v-if="error" class="w-full" role="alert">
          <div class="bg-red-500 text-white font-bold rounded-t px-4 py-2">Error</div>
          <div class="border border-t-0 border-red-400 rounded-b bg-red-100 px-4 py-3 text-red-700">
            <p class="mb-8">Failed to fetch data: {{ error }}</p>
            <button class="btn btn-red" @click="reload()">Retry</button>
          </div>
        </div>
      </div>
    </div>

    <div v-else class="container mt-6">
      <router-view v-if="routerActive"></router-view>
    </div>
  </div>
</template>


<script>
import * as vClickOutside from "v-click-outside-x";

import { mapGetters } from "vuex";

import { ownerSettingsLink } from "@/util/link.js";

export default {
  name: "App",
  directives: {
    clickOutside: vClickOutside.directive
  },
  components: {},
  computed: {
    ...mapGetters(["error", "user"])
  },
  data() {
    return {
      routerActive: true,
      navActive: false,
      userDropdownActive: false,
      createDropdownActive: false
    };
  },
  watch: {
    user: function(user) {
      if (user) {
        this.$router.push({
          name: "user",
          params: { username: this.user.username }
        });
      }
    },
    $route: function() {
      this.userDropdownActive = false;
      this.createDropdownActive = false;
    }
  },
  // method to reload current view from https://github.com/vuejs/vue-router/issues/296#issuecomment-356530037
  methods: {
    ownerSettingsLink: ownerSettingsLink,
    reload() {
      this.$store.dispatch("setError", null);
      this.routerActive = false;
      this.$nextTick(() => (this.routerActive = true));
    }
  }
};
</script>

<style lang="scss">
</style>