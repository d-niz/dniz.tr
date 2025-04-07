<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faDiscord, faInstagram, faMastodon, faTelegram, faSignalMessenger, fakey} from '@fortawesome/free-brands-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';

// FontAwesome ikonlarını kaydetme
library.add(faDiscord, faInstagram, faMastodon, faTelegram, faSignalMessenger,fakey);

const discordStatusColor = ref('text-catppuccin-gray');
const discordStatus = ref('offline');
const spotify = ref(null);
const ws = ref(null);

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
    console.log('Received WebSocket message:', message); // Gelen veriyi göster
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

onMounted(() => {
  connectWebSocket();
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
    deniz (aka dniz or denzz), a normal person, arch enjoyer, idk who i am. Contact: denzz@denzz.xyz
  </div>

  <div class="flex gap-2 items-center text-sm mt-2" :class="discordStatusColor">
    <font-awesome-icon :icon="['fab', 'discord']" class="text-xl w-5 h-5" />
    <div>
      i'm currently {{ discordStatus }} on Discord.
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
    <a href="https://signal.me/#eu/10FRPQwWfQi-MtUfIIBKrjcMynVpyW-UVTrt8KcYdwAOgSPuSwVETbfbJ-WuseF0" target="_blank" class="flex items-center justify-center">
      <font-awesome-icon :icon="['fas', 'key']" class="w-5 h-5" />
    </a>
  </div>
</template>
