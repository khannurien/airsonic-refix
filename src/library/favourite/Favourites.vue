<template>
  <div>
    <ul class="nav-underlined mb-3">
      <li>
        <router-link :to="{... $route, params: { }}">
          Albums
        </router-link>
      </li>
      <li>
        <router-link :to="{... $route, params: { section: 'artists' }}">
          Artists
        </router-link>
      </li>
      <li>
        <router-link :to="{... $route, params: { section: 'tracks' }}">
          Tracks
        </router-link>
      </li>
    </ul>
    <ContentLoader v-slot :loading="details == null">
      <ArtistList v-if="section === 'artists'" :items="details.artists" />
      <TrackList v-else-if="section === 'tracks'" :tracks="details.tracks" />
      <AlbumList v-else :items="details.albums" />
    </ContentLoader>
  </div>
</template>
<script lang="ts">
  import { defineComponent, ref, watch } from 'vue'
  import AlbumList from '@/library/album/AlbumList.vue'
  import ArtistList from '@/library/artist/ArtistList.vue'
  import TrackList from '@/library/track/TrackList.vue'
  import { useFavouriteStore } from '@/library/favourite/store'
  import { useApi } from '@/shared'

  export default defineComponent({
    components: {
      AlbumList,
      ArtistList,
      TrackList,
    },
    props: {
      section: { type: String, default: '' },
    },
    setup() {
      const api = useApi()
      const favouriteStore = useFavouriteStore()
      const details = ref<any>(null)
      watch(
        () => [favouriteStore],
        async() => {
          const result = await api.getFavourites()
          details.value = {
            albums: result.albums.filter((item: any) => favouriteStore.albums[item.id]),
            artists: result.artists.filter((item: any) => favouriteStore.artists[item.id]),
            tracks: result.tracks.filter((item: any) => favouriteStore.tracks[item.id]),
          }
        },
        { deep: true, immediate: true }
      )
      return {
        details
      }
    },
  })
</script>
