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

<style lang="scss" scoped>
@import "~assets/scss/main.scss";

.md-layout-item {
  height: 30vh;
}

.md-card {
  width: 95%;
  height: 95%;
  margin: 16px;
  display: inline-block;
  vertical-align: top;
  border-color: white;
  border-width: 1px;
  border-style: solid;
  border-radius: 25px;
  background: radial-gradient(#e6646500, #00000044) !important;

  .md-card-header {
    height: 12em;
    display: flex;
    justify-content: center;
    align-content: center;
  }

  .md-subhead {
    margin-top: 5px;
  }

  .md-card-header,
  .dates {
    display: flex;
    border-bottom-color: white;
    border-bottom-width: 1px;
    border-bottom-style: solid;
  }

  .dates {
    display: flex;
    justify-content: center;
    margin-top: 0.5em;
    padding-bottom: 0.5em;
    margin-bottom: 0;
  }

  .md-card-header-text {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-content: center;
    margin-left: 1em;
  }

  .line {
    background-image: $default-gradient;
    height: 3px;
    width: 75%;
  }

  .md-card-media {
    display: flex;
    align-items: center;
    div {
      height: max-content;
    }
  }

  .md-list {
    background-color: transparent;
    max-height: 10em !important;
    height: 8em;

    h2 {
      margin-left: 1em;
      margin-top: 0.25em;
      margin-bottom: 0;
    }

    .md-list-item-text {
      font-size: 1.25em;
    }

    .md-icon.md-theme-default {
      background-image: $default-gradient;
      background-clip: text;
      color: transparent;
      font-size: 2em !important;
      margin-right: 0;
    }

    .md-list-item {
      margin-left: 2em;
      height: 2.5em;
    }
  }

  .md-card-actions {
    margin-left: 1em;
  }
}
</style>
