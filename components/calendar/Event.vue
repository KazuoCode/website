<template>
<div class="md-layout-item md-size-50">
	<md-card>
		<md-card-header>
			<div class="logo">
				<img :src="eventLogo" alt="Logo" />
			</div>
			<md-card-header-text>
				<h1 class="md-title">
					{{ event.Series.name }}
				</h1>
				<div class="line" />
				<h2 class="md-subhead">
					{{ event.name }}
				</h2>
			</md-card-header-text>
		</md-card-header>
		<h2 class="dates">
			{{ startdate }} - {{ enddate }}
		</h2>
		<md-list v-if="event.SupportSeries.length > 0">
			<h2>Support Series:</h2>
			<md-list-item v-for="ss in event.SupportSeries" :key="ss.id">
				<md-icon>
					keyboard_arrow_right
				</md-icon>
				<span class="md-list-item-text">{{ ss.Series.name }}</span>
			</md-list-item>
		</md-list>
		<md-card-actions>
			<md-button>Action</md-button>
		</md-card-actions>
	</md-card>
</div>
<!--<md-card>
		<md-card-header>
			<md-card-header-text>
				<div class="md-title">
					{{ event.name }}
				</div>
				<div class="md-subhead">
					{{ event.Track.name }}
				</div>
				<div class="md-subhead">
					{{ startdate }} - {{ enddate }}<br />
					({{ event.Track.timezone }}, UTC{{ offset }})
				</div>
				<div class="md-subhead">
					Priority: {{ event.priority }}
				</div>
				<div v-if="event.SupportSeries.length > 0" class="md-subhead">
					SupportSeries: <span v-for="ss in event.SupportSeries" :key="ss.id">{{ ss.Series.name }},</span>
				</div>
			</md-card-header-text>

			<md-card-media>
				<img :src="eventLogo" alt="Logo" />
			</md-card-media>
		</md-card-header>
		<md-card-content>

		</md-card-content>
		<md-card-actions>
			<md-button @click.native="toggleSessions()">
				Show sessions
			</md-button>
		</md-card-actions>
  </md-card>-->
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
    // addEventSession: function() {
    // 	this.$root.$emit('addEventSession', this.event);
    // },
    toggleSessions: function() {
      this.$root.$emit('toggleSessions', this.event);
    }
  }
};
</script>

<style lang="scss" scoped>
@import '~assets/scss/main.scss';

.md-card {
  width: 95%;
  margin: 16px;
  display: inline-block;
  vertical-align: top;
  border-color: white;
  border-width: 1px;
  border-style: solid;
  border-radius: 25px;

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
    margin-top: .5em;
    padding-bottom: .5em; 
  }

  .logo {
    width: 8em;
    height: auto;
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

  .md-icon.md-theme-default {
    background-image: $default-gradient;
    background-clip: text;
    color: transparent;
    font-size: 2.5em !important;
  }

  .md-list-item-text {
    font-size: 1.5em;
  }

}
</style>
