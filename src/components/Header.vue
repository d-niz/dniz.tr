<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faDiscord, faInstagram, faMastodon, faTelegram, faSignalMessenger, faMusic } from '@fortawesome/free-brands-svg-icons';
import { faKey } from '@fortawesome/free-solid-svg-icons';
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
    console.log('WebSocket connected');
    ws.value.send(JSON.stringify({
      op: 2,
      d: { subscribe_to_id: '1259949511078318287' }
    }));
  };

  ws.value.onmessage = (event) => {
    const message = JSON.parse(event.data);
    console.log('Received WebSocket message:', message);
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
  const API_KEY = '25456cc62de7291306a1fe391ea550b9'; // 🔧 Last.fm API key'ini buraya yaz
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

<template>
  <div class="font-sans font-black text-5xl">
    dniz.tr
  </div>
  <div>
    deniz (aka dniz or denzz), a normal person, arch enjoyer, idk who i am. contact: denzz@denzz.xyz
  </div>

  <div class="flex gap-2 items-center text-sm mt-2" :class="discordStatusColor">
    <font-awesome-icon :icon="['fab', 'discord']" class="text-xl w-5 h-5" />
    <div>
      i'm currently {{ discordStatus }} on Discord.
    </div>
  </div>

  <!-- 🎵 Last.fm Durumu -->
  <div class="flex gap-2 items-center text-sm mt-2" :class="lastfmColor">
    <font-awesome-icon icon="music" class="text-xl w-5 h-5" />
    <div>
      <span v-if="lastfmTrack">i'm listening {{ lastfmTrack }} right now.</span>
      <span v-else>i'm not listening anything.</span>
    </div>
  </div>

  <div class="flex gap-10 mt-5 text-xl">
    <a href="https://www.instagram.com/msadenz.z" target="_blank" class="flex items-center justify-center">
      <font-awesome-icon :icon="['fab', 'instagram']" class="w-5 h-5" />
    </a>
    <a href="https://discord.com/user/1259949511078318287" target="_blank" class="flex items-center justify-center">
      <font-awesome-icon :icon="['fab', 'discord']" class="w-5 h-5" />
    </a>
    <a href="https://mastodon.com.tr/@denzz" target="_blank" class="flex items-center justify-center">
      <font-awesome-icon :icon="['fab', 'mastodon']" class="w-5 h-5" />
    </a>
    <a href="https://t.me/use_rname" target="_blank" class="flex items-center justify-center">
      <font-awesome-icon :icon="['fab', 'telegram']" class="w-5 h-5" />
    </a>
    <a href="https://signal.me/#eu/10FRPQwWfQi-MtUfIIBKrjcMynVpyW-UVTrt8KcYdwAOgSPuSwVETbfbJ-WuseF0" target="_blank" class="flex items-center justify-center">
      <font-awesome-icon :icon="['fab', 'signal-messenger']" class="w-5 h-5" />
    </a>
    <a href="https://keys.openpgp.org/vks/v1/by-fingerprint/0FF28A34E3098C31CEAD6034433B49A88191C661" target="_blank" class="flex items-center justify-center">
      <font-awesome-icon :icon="['fas', 'key']" class="w-5 h-5" />
    </a>
  </div>
</template>
