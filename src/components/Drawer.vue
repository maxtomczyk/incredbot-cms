<template>
  <div class="drawer__component">
    <div class="drawer__container container">
      <div class="row">
        <div class="drawer col-md-4 col-lg-3 col-xs-9" :class="{'drawer--closed': !opened}">
          <div class="drawer__items">
            <drawer-item
              v-show="allowedViews.indexOf('Dashboard') !== -1"
              icon="home"
              target="/"
              @clicked="close()"
            >Dashboard</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Messages') !== -1"
              icon="comments"
              target="/messages"
              @clicked="close()"
            >Content</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Chat Requests') !== -1"
              icon="user-clock"
              target="/chats"
              @clicked="close()"
            >Chat requests</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Custom keywords') !== -1"
              icon="atlas"
              target="/keywords"
              @clicked="close()"
            >Keywords</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Custom Postbacks') !== -1"
              icon="expand-arrows-alt"
              target="/postbacks"
              @clicked="close()"
            >Postbacks</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Unknown phrases') !== -1"
              icon="question"
              target="/unknown_phrases"
              @clicked="close()"
            >New phrases</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Message broadcast') !== -1"
              icon="broadcast-tower"
              target="/emissions"
              @clicked="close()"
            >Emissions</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Attachments') !== -1"
              icon="paperclip"
              target="/attachments"
              @clicked="close()"
            >Attachments</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Admins') !== -1"
              icon="user-shield"
              target="/admins"
              @clicked="close()"
            >Admins</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Bot Elements') !== -1"
              icon="list"
              target="/elements"
              @clicked="close()"
            >Bot elements</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf('Clicks') !== -1"
              icon="mouse-pointer"
              target="/clicks"
              @clicked="close()"
            >Clicks</drawer-item>
            <drawer-item
              v-show="allowedViews.indexOf(route.name) !== -1"
              v-for="route in customRoutes"
              :key="`DRAWER-${route.name}`"
              :icon="route.icon"
              :target="route.route"
              @clicked="close()"
            >{{ route.name }}</drawer-item>
          </div>

          <div class="drawer__list">
            <drawer-list-item v-show="allowedViews.indexOf('Dashboard') !== -1" icon="home" target="/" @clicked="close()">Dashboard</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Messages') !== -1" icon="comments" target="/messages" @clicked="close()">Content</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Chat Requests') !== -1" icon="user-clock" target="/chats" @clicked="close()">Chat requests</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Custom keywords') !== -1" icon="atlas" target="/keywords" @clicked="close()">Keywords</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Custom Postbacks') !== -1"
              icon="expand-arrows-alt"
              target="/postbacks"
              @clicked="close()"
            >Postbacks</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Unknown phrases') !== -1"
              icon="question"
              target="/unknown_phrases"
              @clicked="close()"
            >New phrases</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Message broadcast') !== -1"
              icon="broadcast-tower"
              target="/emissions"
              @clicked="close()"
            >Emissions</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Attachments') !== -1" icon="paperclip" target="/attachments" @clicked="close()">Attachments</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Admins') !== -1" icon="user-shield" target="/admins" @clicked="close()">Admins</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Bot Elements') !== -1" icon="list" target="/elements" @clicked="close()">Bot elements</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf('Clicks') !== -1" icon="mouse-pointer" target="/clicks" @clicked="close()">Clicks</drawer-list-item>
            <drawer-list-item v-show="allowedViews.indexOf(route.name) !== -1"
              v-for="route in customRoutes"
              :key="`DRAWER-LIST-${route.name}`"
              :icon="route.icon"
              :target="route.route"
              @clicked="close()"
            >{{ route.name }}</drawer-list-item>
          </div>
        </div>
        <div v-if="opened" class="drawer__overlay col-md-8 col-lg-9 col-xs-3" @click="close()"></div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import userRoutes from './UserDefinedViewsLinks'

export default {
  data () {
    return {
      opened: false,
      customRoutes: userRoutes,
      allowedViews: []
    }
  },

  methods: {
    open () {
      this.opened = true
      this.$emit('opened')
    },

    close () {
      this.opened = false
      this.$emit('closed')
    },

    toggle () {
      if (this.opened) this.close()
      else this.open()
    }
  },

  async created () {
    try {
      axios.defaults.headers.common['Authorization'] = 'Bearer ' + localStorage.getItem('token')
      let allowed = ['Login', 'Logout', 'Dashboard', 'Admins']
      const adminViewsReq = await axios.get('/api/admins/views')
      this.allowedViews = allowed.concat(adminViewsReq.data)
    } catch (e) {
      console.error(e)
    }
  }
}
</script>

<style lang="scss">
@import "../styles/variables";

.drawer {
  position: fixed;
  width: 100vw;
  height: 100vh;
  background-color: $bg-accent;
  z-index: 10;
  -webkit-box-shadow: 1px 1px 14px -2px rgba(0, 0, 0, 0.75);
  -moz-box-shadow: 1px 1px 14px -2px rgba(0, 0, 0, 0.75);
  box-shadow: 1px 1px 14px -2px rgba(0, 0, 0, 0.75);
  left: 0;
  transition: left 0.5s ease;
  will-change: left;
  padding-top: 70px;
  padding-left: 0;
  padding-right: 0;

  &--closed {
    left: -100vw;
  }

  &__row {
    background-color: red;
  }

  &__overlay {
    position: fixed;
    height: 100vh;
    width: 100vw;
    right: 0;
    z-index: 10;
  }

  &__container {
    position: absolute;
    left: 8px;
    width: 100vw;
    padding: 0;
  }

  &__items {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-auto-columns: 1fr;
    padding: 15px 12px;
    grid-row-gap: 40px;
    max-height: 86vh;
    overflow-y: scroll;

    &::-webkit-scrollbar {
      width: 2px;
    }

    &::-webkit-scrollbar-thumb {
      background-color: $font-primary;
      border-radius: 2px;
    }
  }

  &__list {
    max-height: 90%;
    overflow-y: scroll;

    &::-webkit-scrollbar {
      width: 2px;
    }

    &::-webkit-scrollbar-thumb {
      background-color: $font-primary;
      border-radius: 2px;
    }
  }

  &__footer {
    text-align: center;
    position: absolute;
    bottom: 2px;
    width: 100%;
  }

  &__footer-logo {
    font-family: "Major Mono Display", monospace;
    font-size: 1.1em;
    color: #fff;
    margin-bottom: 0;
  }

  &__footer-info {
    color: $font-primary;
    display: block;
    font-family: "Major Mono Display", monospace;
    font-size: 0.6em;
    font-style: italic;
    margin-top: -3px;
  }
}

@media only screen and (max-width: 768px) {
  .drawer__items {
    display: none;
  }
}

@media only screen and (min-width: 769px) {
  .drawer__list {
    display: none;
  }
}

@media only screen and (max-height: 800px) and (min-width: 700px) {
  .drawer__footer {
    display: none;
  }
}
</style>
