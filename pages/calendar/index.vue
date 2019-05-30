<template>
<div class="md-layout">
	<FilterPanel
		:show-current-events="showCurrentEvents"
		@toggleCurrentEvents="toggleCurrentEvents"
	/>

	<div class="md-layout-item flex-start">
		<div class="headline">
			<span class="md-display-1">{{ headline }}</span><br />
		</div>
		<div class="md-layout">
			<Event
				v-for="event in filterEvents()"
				:key="event.id"
				:event="event"
				:tz="data.tz"
				:active-event="activeEvent"
				@toggleSessions="toggleSessions"
			/>
		</div>
	</div>

	<SidePanel
		:event="activeEvent"
		:show-event="showEvent"
		:tz="data.tz"
		@toggleSessions="toggleSessions"
	/>
</div>
</template>

<script>
import Event from '~/components/calendar/Event.vue';
import FilterPanel from '~/components/calendar/FilterPanel.vue';
import SidePanel from '~/components/calendar/SidePanel.vue';

import moment from 'moment-timezone';

export default {
	components: {
		Event,
		FilterPanel,
		SidePanel
	},
	data: function() {
		return {
			data: [],
			showCurrentEvents: true,
			activeEvent: null,
			showEvent: false
		};
	},
	computed: {
		headline: function() {
			return this.showCurrentEvents ? 'This week\'s events' : 'All events';
		}
	},
	async asyncData({
		$axios
	}) {
		const resdata = await $axios.$get('/api/calendar');
		return {
			data: resdata
		};
	},
	methods: {
		toggleSessions: function(event) {
			if (!this.showEvent) {
				this.showEvent = true;
				this.activeEvent = event;
			} else {
				this.showEvent = false;
				this.activeEvent = null;
			}
		},
		toggleCurrentEvents: function() {
			this.showCurrentEvents = !this.showCurrentEvents;
		},
		filterEvents: function() {
			if (this.showCurrentEvents)
				return this.data.events.filter(function(event) {
					return moment(event.startdate).isBefore('2019-07-01');
				});
			else
				return this.data.events;
		}
	}
};
</script>

<style lang="scss" scoped>
.flex-start {
	align-content: flex-start;
}
.headline {
	padding: 1em;
}
</style>