<template>
  <div class="pb-3 mb-2 installed-app d-flex flex-column align-items-center">
    <a
      class="d-block mb-3 installed-app-link"
      :href="url"
      target="_blank"
      :class="isUninstalling ? 'fade-in-out' : ''"
      :disabled="isUninstalling"
      ><img
        class="installed-app-icon app-icon"
        :alt="name"
        :src="`https://getumbrel.github.io/umbrel-apps-gallery/${id}/icon.svg`"
    /></a>
    <span class="text-center text-truncate mb-1">{{
      isUninstalling ? "Uninstalling..." : name
    }}</span>
    <b-button
      class="uninstall-btn"
      v-if="showUninstallButton && !isUninstalling"
      variant="outline-danger"
      size="sm"
      @click="uninstall(name, id)"
      ><small><b-icon icon="trash"></b-icon> Uninstall</small></b-button
    >
  </div>
</template>

<script>
import { mapState } from "vuex";

export default {
  props: {
    id: String,
    name: String,
    hiddenService: String,
    port: Number,
    path: String,
    showUninstallButton: {
      type: Boolean,
      default: false
    },
    isUninstalling: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {};
  },
  computed: {
    ...mapState({
      installedApps: state => state.apps.installed
    }),
    url: function() {
      if (window.location.origin.indexOf(".onion") > 0) {
        return `http://${this.hiddenService}${this.path}`;
      } else {
        return `http://${window.location.hostname}:${this.port}${this.path}`;
      }
    }
  },
  methods: {
    uninstall(name, appId) {
      if (
        !window.confirm(
          `Are you sure you want to uninstall ${name}? This is will also delete all of its data.`
        )
      ) {
        return;
      }
      this.$store.dispatch("apps/uninstall", appId);
    }
  },
  components: {}
};
</script>

<style lang="scss" scoped>
.installed-app {
  width: 200px;
  position: relative;
  .installed-app-link {
    text-decoration: none;
    .installed-app-icon {
      box-shadow: 0px 10px 30px rgba(209, 213, 223, 0.5);
    }
  }
  .uninstall-btn {
    position: absolute;
    bottom: 0;
  }
}
</style>
