<template>
<div class="md-layout">
	<md-tabs md-alignment="fixed" :md-dynamic-height="true">
		<md-tab id="tab-events" md-label="Events">
			<EventsContainer
				:events="data.events"
				:series="data.series"
				:tracks="data.tracks"
			/>
		</md-tab>
		<md-tab id="tab-tracks" md-label="Tracks">
			<TracksContainer
				:tracks="data.tracks"
				:tz="data.tz"
			/>
		</md-tab>
		<md-tab id="tab-series" md-label="Series">
			<SeriesContainer
				:series="data.series"
			/>
		</md-tab>
	</md-tabs>
</div>
</template>

<script>
import EventsContainer from '~/components/resources/EventsContainer.vue';
import SeriesContainer from '~/components/resources/SeriesContainer.vue';
import TracksContainer from '~/components/resources/TracksContainer.vue';

export default {
	components: {
		EventsContainer, SeriesContainer, TracksContainer
	},
	data: function() {
		return {
			data: []
		};
	},
	async asyncData({
		$axios
	}) {
		const resdata = await $axios.$get('/api/calendar');
		return {
			data: resdata
		};
	},
	mounted() {
		this.$root.$on('eventCreated', obj => {
			this.data.events.push(obj);
			this.data.events.sort((a,b) => {
				if (a.priority === b.priority)
					return a.startdate.localeCompare(b.startdate);
				else
					return a.priority - b.priority;
			});
			this.$root.$emit('showToast', 'Event ' + obj.name + ' created');
		});
		this.$root.$on('eventUpdated', event => {
			let index = this.data.events.findIndex(e => e.id == event.id);
			this.data.events.splice(index, 1, event);
			this.data.events.sort((a,b) => {
				if (a.priority === b.priority)
					return a.startdate.localeCompare(b.startdate);
				else
					return a.priority - b.priority;
			});
			this.$root.$emit('showToast', 'Event ' + event.name + ' updated');
		});
		this.$root.$on('eventDeleted', eventid => {
			let index = this.data.events.findIndex(e => e.id == eventid);
			let event = this.data.events.find(e => e.id == eventid);
			this.data.events.splice(index, 1);
			this.$root.$emit('showToast', 'Event ' + event.name + ' deleted');
		});
		// EventSessions
		this.$root.$on('eventSessionCreated', session => {
			let event = this.data.events.find(e => e.id == session.event);
			event.EventSessions.push(session);
			event.EventSessions.sort((a,b) => {
				return a.starttime.localeCompare(b.starttime);
			});
			this.$root.$emit('showToast', 'Session ' + session.name + ' created');
		});
		this.$root.$on('eventSessionUpdated', session => {
			let event = this.data.events.find(e => e.id == session.event);
			let sessionindex = event.EventSessions.findIndex(s => s.id == session.id);
			event.EventSessions.splice(sessionindex, 1, session);
			event.EventSessions.sort((a,b) => {
				return a.starttime.localeCompare(b.starttime);
			});
			this.$root.$emit('showToast', 'Session ' + session.name + ' updated');
		});
		this.$root.$on('eventSessionDeleted', (eventid, sessionid) => {
			let event = this.data.events.find(e => e.id == eventid);
			let sessionindex = event.EventSessions.findIndex(s => s.id == sessionid);
			let session = event.EventSessions.find(s => s.id == sessionid);
			event.EventSessions.splice(sessionindex, 1);
			this.$root.$emit('showToast', 'Session ' + session.name + ' deleted');
		});
		this.$root.$on('seriesCreated', obj => {
			this.data.series.push(obj);
			this.data.series.sort((a,b) => {
				if (a.priority === b.priority)
					return a.name.localeCompare(b.name);
				else
					return a.priority - b.priority;
			});
			this.$root.$emit('showToast', 'Series ' + obj.name + ' created');
		});
		this.$root.$on('seriesUpdated', updatedSeries => {
			let index = this.data.series.findIndex(s => s.id == updatedSeries.id);
			this.data.series.splice(index, 1, updatedSeries);
			this.data.series.sort((a,b) => {
				if (a.priority === b.priority)
					return a.name.localeCompare(b.name);
				else
					return a.priority - b.priority;
			});
			this.$root.$emit('showToast', 'Series ' + updatedSeries.name + ' updated');
		});
		this.$root.$on('seriesDeleted', seriesid => {
			let index = this.data.series.findIndex(s => s.id == seriesid);
			let series = this.data.series.find(s => s.id == seriesid);
			this.data.series.splice(index, 1);
			this.$root.$emit('showToast', 'Series ' + series.name + ' deleted');
		});
		this.$root.$on('trackCreated', obj => {
			this.data.tracks.push(obj);
			this.data.tracks.sort((a,b) => {
				return a.name.localeCompare(b.name);
			});
			this.$root.$emit('showToast', 'Track ' + obj.name + ' created');
		});
		this.$root.$on('trackUpdated', updatedTrack => {
			let index = this.data.tracks.findIndex(t => t.id == updatedTrack.id);
			this.data.tracks.splice(index, 1, updatedTrack);
			this.$root.$emit('showToast', 'Track ' + updatedTrack.name + ' updated');
		});
		this.$root.$on('trackDeleted', trackid => {
			let index = this.data.tracks.findIndex(t => t.id == trackid);
			let track = this.data.tracks.find(t => t.id == trackid);
			this.data.tracks.splice(index, 1);
			this.$root.$emit('showToast', 'Track ' + track.name + ' deleted');
		});
	}
};
</script>

<style lang="scss" scoped>
.main-panel {
	// margin-right: 1em !important;
	padding-top: 2em;
}
.md-layout {
	margin: 2em 10em 2em 10em !important;
}
.md-tabs {
	margin: 0 0 0 0 !important;
}
.md-tabs {
	width: 100%;
}
</style>
