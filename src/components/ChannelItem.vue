<template>
  <div
    class="channel"
    :class='channel.type === "additional" ? "" : "channel-driver"'
    draggable="true"
    unselectable="on"
    :style="{ 'border-color': channel.hex }"
    @click="replaceFullscreen"
  >
    <div v-if='channel.type === "additional"'>
      {{ channel.title }}
    </div>
    <div v-else>
      <span class="driver-tag">{{ channel.racingNumber }} {{ channel.title }}</span>
      <span class="driver-name">{{ channel.driverFirstName }} {{ channel.driverLastName }}</span>
    </div>
  </div>
</template>

<script>
  import { v4 as uuidv4 } from "uuid";
  import { mapGetters, mapActions } from "vuex";
  
    props: {
      channel: {
        type: Object,
        required: true
      }
    },
    data() {
      return {
        mousePos: {},
        dragPos: {}
      };
    },
    computed: {
      ...mapGetters(["layout"])
    },
    methods: {
      replaceFullscreen() {
        const gridLayout = this.$root.$refs.gridLayout;
        const props = gridLayout.$options.propsData;
        this.setLayout([{
          x: 0,
          y: 0,
          w: props.colNum,
          h: Math.floor(screen.availHeight / props.rowHeight),
          i: 0,
          options: {
            controls: true,
            muted: true,
            liveui: true,
            preload: "auto",
            crossOrigin: "anonymous",
          },
          playbackUrl: this.channel.playbackUrl,
          title: this.channel.title,
          live: this.channel.live
        }]);
      },
      dragOver(e) {
        this.mousePos.x = e.clientX;
        this.mousePos.y = e.clientY;
      },
      mouseInGrid(mouse, rect) {
        return mouse.x > rect.left && mouse.x < rect.right && mouse.y > rect.top && mouse.y < rect.bottom;
      },
      ...mapActions(["addToLayout", "setLayout"])
    },
    mounted() {
      document.addEventListener("dragover", this.dragOver);
    }
  };
</script>

<style lang="scss" scoped>

  .channel {
    margin-bottom: 0.5em;
    padding: 5px 0.5em;
    border-left: 5px solid;
  }

  .channel-driver {
    padding: 2px 0.5em;
    width: 50%;
    float: left;
  }

  .channel:hover {
    background: #f8f8f8;
    cursor: pointer;
  }

  .channel:active {
    cursor: pointer;
  }

  .driver-tag {
    display: block;
    font-size: 14px;
  }

  @media screen and (max-width: 768px) {
    .driver-name {
      display: none;
    }
  }
</style>
