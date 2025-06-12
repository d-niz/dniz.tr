// tamamen yapay zeka kullandım kod iyi olmayabilir ama çalısa yeter vue ogrenecek zamanim yok vue bilmiom
<template>
  <div class="space-y-2">
    <div class="flex items-center gap-2">
      <font-awesome-icon icon="discord" />
      <span :class="discordStatusColor">Discord: {{ discordStatus }}</span>
    </div>

    <div class="flex items-center gap-2">
      <font-awesome-icon icon="music" />
      <span :class="lastfmColor">
        <template v-if="lastfmTrack">
          {{ lastfmTrack }}
        </template>
        <template v-else>
          Dinlenmiyor
        </template>
      </span>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faDiscord, faInstagram, faMastodon, faTelegram, faSignalMessenger } from '@fortawesome/free-brands-svg-icons';
import { faKey, faMusic } from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

library.add(faDiscord, faInstagram, faMastodon, faTelegram, faSignalMessenger, faKey, faMusic);

const discordStatusColor = ref('text-catppuccin-gray');
const discordStatus = ref('offline');
const spotify = ref(null);
const ws = ref(null);

const lastfmTrack = ref(null);
const lastfmColor = ref('text-catppuccin-gray');

const connectWebSocket = () => {
  ws.value = new WebSocket('wss://api.lanyard.rest/socket');

  ws.value.onopen = () => {
    ws.value.send(JSON.stringify({
      op: 2,
      d: { subscribe_to_id: '1259949511078318287' }
    }));
  };

  ws.value.onmessage = (event) => {
    const message = JSON.parse(event.data);
    if (message.t === "INIT_STATE" || message.t === "PRESENCE_UPDATE") {
      const data = message.d;
      spotify.value = data.spotify;

      switch (data.discord_status) {
        case 'online':
          discordStatusColor.value = 'text-catppuccin-green';
          discordStatus.value = 'online';
          break;
        case 'idle':
          discordStatusColor.value = 'text-catppuccin-yellow';
          discordStatus.value = 'idle';
          break;
        case 'dnd':
          discordStatusColor.value = 'text-catppuccin-red';
          discordStatus.value = 'do not disturb';
          break;
        default:
          discordStatusColor.value = 'text-catppuccin-gray';
          discordStatus.value = 'offline';
          break;
      }
    }
  };

  ws.value.onerror = (error) => {
    console.error('WebSocket error', error);
  };

  ws.value.onclose = () => {
    console.log('WebSocket connection closed');
  };
};

const fetchLastFmNowPlaying = async () => {
  const API_KEY = '25456cc62de7291306a1fe391ea550b9';
  const USERNAME = 'den-zz';
  const url = `https://ws.audioscrobbler.com/2.0/?method=user.getrecenttracks&user=${USERNAME}&api_key=${API_KEY}&format=json&limit=1`;

  try {
    const response = await fetch(url);
    const data = await response.json();
    const track = data.recenttracks.track[0];

    if (track && track['@attr'] && track['@attr'].nowplaying === 'true') {
      lastfmTrack.value = `${track.artist['#text']} - ${track.name}`;
      lastfmColor.value = 'text-catppuccin-mauve';
    } else {
      lastfmTrack.value = null;
      lastfmColor.value = 'text-catppuccin-gray';
    }
  } catch (error) {
    console.error('Last.fm API hatası:', error);
    lastfmTrack.value = null;
    lastfmColor.value = 'text-catppuccin-gray';
  }
};

onMounted(() => {
  connectWebSocket();
  fetchLastFmNowPlaying();
  setInterval(fetchLastFmNowPlaying, 30000);
});

onUnmounted(() => {
  if (ws.value) {
    ws.value.close();
  }
});
</script>
