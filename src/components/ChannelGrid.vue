<template>
  <div style="clear: both">
    <h2 class="subtitle has-text-centered">Channels</h2>
    <div class="container">
      <ChannelItem v-for="(channel, i) in channelsSorted" :key="i" :channel="channel" />
    </div>
    <div style="clear: both" />
  </div>
</template>

<script>
  import ChannelItem from "@/components/ChannelItem";

  export default {
    name: "ChannelGrid",
    components: {
      ChannelItem
    },
    props: {
      channels: {
        type: Array,
        required: true
      }
    },
    computed: {
      channelsSorted() {
        const additional = [];
        const onBoards = [];
        for (let channel of this.channels) {
          if (channel.type === "additional") {
            additional.push(channel);
          } else {
            onBoards.push(channel);
          }
        }

        return additional.concat(onBoards.sort((val1, val2) => {
          if (val1.teamName !== val2.teamName) {
            return val1.teamName.localeCompare(val2.teamName);
          }
          return val1.racingNumber - val2.racingNumber;
        }));
      }
    },
  };
</script>

<style lang="scss" scoped>
  .container {
    padding-bottom: 2em;
  }

  .subtitle {
    padding-top: 0.5em;
  }
</style>
