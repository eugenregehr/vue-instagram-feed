<template>
  <div class="instagram-wrapper">
    <Loader v-if="!imagesLoaded"/>
    <div class="instagram-feed">
      <slot :feed="feeds" name="feeds" />
      <div class="has-text-centered">
        <button
          class="button is-normal is-rounded is-primary mb-4"
          @click="increaseCount"
        >
          Load More
        </button>
      </div>
    </div>
    <slot name="error" :error="error" />
  </div>
</template>

<script>
// import Loader from "~/components/fragments/Loader.vue";
import axios from "axios";

export default {
  components: {
    Loader,
  },
  props: {
    token: {
      type: String,
      required: true,
    },
    fields: {
      type: String,
      required: true,
    },
    mediatypes: {
      type: Array,
      required: true,
    },
    imagesLoaded: {
      type: Boolean,
      default: false,
    },
  },
  data: () => ({
    error: null,
    feeds: [],
    count: 12,
  }),
  mounted() {
    this.getUserFeed();
  },
  methods: {
    increaseCount() {
      this.count += 6;
      this.getUserFeed();
    },
    getUserFeed() {
      axios
        .get("https://graph.instagram.com/me/media", {
          params: { access_token: this.token, fields: this.fields },
        })
        .then((response) => {
          if (response.status === 400) {
            this.error = response.error.message;
          }
          if (response.status === 200) {
            for (const item in response.data.data) {
              // get only custom media type
              if (
                this.mediatypes.includes(response.data.data[item].media_type)
              ) {
                this.feeds.push(response.data.data[item]);
                if (this.feeds.length >= this.count) {
                  return;
                }
              }
            }
          }
        })
        .catch((error) => {
          throw error;
        });
    },
  },
};
</script>

<style lang="scss">
</style>