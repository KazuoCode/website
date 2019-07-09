<template>
<md-card class= "sidepanel">
	<md-card-header>
		<md-card-media md-medium>
			<div>
				<img :src="event.logo" alt="Logo" />
			</div>
		</md-card-media>
		<md-card-header-text>
			<h1 class="md-title">
				{{ event.Series.name }}
			</h1>
			<div class="line" />
			<h2 class="md-subhead">
				{{ event.Track.name }}, {{ event.Track.country }}
			</h2>
		</md-card-header-text>
	</md-card-header>
	<h2 class="dates">
		{{ event.name }}
	</h2>
	<md-tabs md-alignment="centered">
		<md-tab id="tab-main" md-label="Schdule" />
		<md-tab id="tab-track" md-label="Track" />
		<md-tab id="tab-more" md-label="More" />
	</md-tabs>
</md-card>
<!--<div v-if="showEvent" class="sessions">
		<md-icon class="md-size-2x" @click.native="toggleSessions()">
			arrow_back
		</md-icon>
		<br />
		<br />
		<EventSession
			v-for="session in event.EventSessions"
			:key="session.id"
			:session="session"
			:local-timezone="event.Track.timezone"
			:user-timezone="userTimezone"
		/>
  </div>-->
</template>

<script>
//import EventSession from '~/components/calendar/EventSession.vue';

export default {
  components: {
    //EventSession
  },
  props: {
    event: {
      type: Object,
      default: function() {
        return {
          name: '',
          logo: '',
          Series: {
            name: ''
          },
          Track: {
            name: '',
            country: ''
          }
        };
      }
    },
    showEvent: {
      type: Boolean,
      default: false
    },
    userTimezone: {
      type: Object,
      default: function() {
        return {
          name: '',
          desc: ''
        };
      }
    }
  },
  methods: {
    toggleSessions: function() {
      this.$root.$emit('toggleSessions', null);
    }
  }
};
</script>