<template>
<div class="md-layout-item md-size-33">
	<md-card>
		<md-card-header>
			<md-card-media md-medium>
				<div>
					<img :src="eventLogo" alt="Logo" />
				</div>
			</md-card-media>
			<md-card-header-text>
				<h1 class="md-title">
					{{ event.Series.name }}
				</h1>
				<div class="line" />
				<h2 class="md-subhead">
					{{ event.name }} <br />
					{{ event.Track.name }}, {{ event.Track.country }}
				</h2>
			</md-card-header-text>
		</md-card-header>
		<h2 class="dates">
			{{ startdate }} - {{ enddate }}
		</h2>
		<md-list v-if="event.SupportSeries.length > 0">
			<h2>Support Series:</h2>
			<md-list-item v-for="ss in prioritySupportSeries(event)" :key="ss.id">
				<md-icon>keyboard_arrow_right</md-icon>
				<span class="md-list-item-text">{{ ss.Series.name }}</span>
			</md-list-item>
		</md-list>
		<md-list v-if="event.SupportSeries.length === 0">
			<h2>Official Website</h2>
			<md-list-item>
				<md-icon>keyboard_arrow_right</md-icon>
				<span class="md-list-item-text"><a :href="event.Series.homepage">{{ event.Series.name }}</a></span>
			</md-list-item>
		</md-list>
		<md-card-actions class="md-alignment-left">
			<md-button>Show Details</md-button>
		</md-card-actions>
	</md-card>
</div>
</template>

<script>
import moment from 'moment-timezone';

export default {
  props: {
    event: {
      type: Object,
      default: null
    },
    tz: {
      type: Object,
      default: null
    },
    activeEvent: {
      type: Object,
      default: null
    }
  },
  computed: {
    startdate: function() {
      return moment(this.event.startdate).format('Do MMM');
    },
    enddate: function() {
      return moment(this.event.enddate).format('Do MMM');
    },
    eventLogo: function() {
      if (this.event.logo.length > 1) return this.event.logo;
      else if (this.event.Series.logo.length > 1) return this.event.Series.logo;
      else return '';
    },
    offset: function() {
      return moment.tz(this.event.Track.timezone).format('Z');
    }
  },
  methods: {
    toggleSessions: function() {
      this.$root.$emit('toggleSessions', this.event);
    },
    prioritySupportSeries: function(event) {
        return event.SupportSeries.sort((a, b) => {
          if(a.Series.priority < b.Series.priority) return -1;
        if(a.Series.priority > b.Series.priority) return 1;
        return 0;
      });
    }
  }
};
</script>

