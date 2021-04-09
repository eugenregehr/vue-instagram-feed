<template>
  <div>
    <InstaFeed
      token="-token-"
      fields="media_url,media_type,caption,thumbnail_url"
      :mediatypes="['IMAGE']"
      :imagesLoaded="imagesLoaded"
    >
      <template v-slot:feeds="props" v-if="loadFeed">
        <div class="columns is-multiline is-mobile">
          <div
            class="column is-6"
            v-for="(item, index) in props.feed"
            :key="`instaImages${index}`"
          >
            <div
              @click="(modalActive = true), (lightboxImage = item)"
              class="overlay is-clickable"
            >
              <img
                :src="item.media_url"
                alt="instagram post"
                @load="onImageLoad(index)"
              />
            </div>
          </div>
        </div>
      </template>
      <template v-slot:error="props">
        <div class="fancy-alert">
          {{ props.error }}
        </div>
      </template>
    </InstaFeed>

    <div v-model="modalActive">
      <div class="insta-lightbox" v-if="lightboxImage">
        <div class="columns">
          <div class="column">
            <img :src="lightboxImage.media_url" alt="Instagram Post Image" />
          </div>
          <div class="column is-narrow">
            <div class="description" v-html="lightboxImage.caption" />
          </div>
        </div>
      </div>
    </div>

  </div>
</template>


<script>
import InstaFeed from "InstaFeed.vue";

export default {
  components: { InstaFeed },
  data() {
    return {
      allImageCount: InstaFeed.data().count,
      imageIndex: [],
      imagesLoaded: false,
      lightboxImage: null,
      modalActive: false,
    };
  },
  methods: {
    onImageLoad(index) {
      this.imageIndex.push(index);
      if (this.imageIndex.length == this.allImageCount) {
        this.imagesLoaded = true;
      }
    },
  }
}
</script>