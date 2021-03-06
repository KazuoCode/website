<template>
<div class="md-layout-item flex-start main-panel">
	<div class="md-layout headline">
		<div class="md-layout-item md-display-1">
			Events
		</div>
		<div class="md-layout-item align-right">
			<md-button class="md-raised md-primary btn-primary" @click.native="createEvent()">
				Create Event
			</md-button>
		</div>
	</div>
	<md-button class="md-raised md-primary" :class="showCurrentEvents ? 'btn-primary' : 'btn-secondary'" @click.native="showCurrentEvents = true">
		Current Events
	</md-button>
	<md-button class="md-raised md-primary" :class="showCurrentEvents ? 'btn-secondary' : 'btn-primary'" @click.native="showCurrentEvents = false">
		Past events
	</md-button>

	<md-list v-if="events.length > 0">
		<md-list-item v-for="e in filterEvents" :key="e.id">
			<div class="md-list-item-text">
				<span @click="toggleSessions(e.id)">
					<strong>{{ e.name }}</strong><br />
					{{ e.startdate }} - {{ e.enddate }}
				</span>
				<span v-if="shownSessions.includes(e.id) && e.EventSessions.length" class="sessions">
					<md-list>
						<md-list-item v-for="s in e.EventSessions" :key="s.id">
							<strong>{{ sessionStart(s.starttime) }} {{ s.name }} ({{ s.Series.name }})</strong>
							<md-icon @click.native="updateEventSession(e, s)">
								edit
							</md-icon>
							<md-icon @click.native="deleteSession(e.id, s.id)">
								delete
							</md-icon>
						</md-list-item>
					</md-list>
				</span>
			</div>
			<md-icon @click.native="createEventSession(e)">
				add_circle
			</md-icon>
			<md-icon @click.native="updateEvent(e)">
				edit
			</md-icon>
			<md-icon @click.native="deleteEvent(e.id)">
				delete
			</md-icon>
		</md-list-item>
	</md-list>

	<CRUDEvent
		:show-dialog="showEventDialog"
		:active-event="activeEvent"
		:series="series"
		:tracks="tracks"
		:mode="mode"
	/>
	<CRUDEventSession
		:show-dialog="showSessionDialog"
		:event="activeEvent"
		:active-session="activeEventSession"
		:mode="mode"
	/>
</div>
</template>

<script>
import CRUDEvent from '~/components/resources/CRUD-Event.vue';
import CRUDEventSession from '~/components/resources/CRUD-EventSession.vue';
import moment from 'moment';

export default {
	components: {
		CRUDEvent, CRUDEventSession
	},
	props: {
		events: {
			type: Array,
			default() { return []; }
		},
		series: {
			type: Array,
			default() { return []; }
		},
		tracks: {
			type: Array,
			default() { return []; }
		}
	},
	data: function() {
		return {
			showEventDialog: false,
			showSessionDialog: false,
			shownSessions: [],
			activeEvent: null,
			activeEventSession: null,
			showCurrentEvents: true,
			mode: ''
		};
	},
	computed: {
		filterEvents() {
			if (this.showCurrentEvents === true)
				return this.events.filter(event => {
					return moment(event.enddate).isSameOrAfter(moment());
				});
			else
				return this.events.filter(event => {
					return moment(event.enddate).isBefore(moment());
				});
		}
	},
	mounted() {
		this.$root.$on('toggleCrudEvent', () => {
			this.showEventDialog = !this.showEventDialog;
		});
		this.$root.$on('toggleCrudEventSession', () => {
			this.showSessionDialog = !this.showSessionDialog;
		});
		this.$root.$on('eventCreated', event => {
			this.activeEvent = event;
			this.showSessionDialog = !this.showSessionDialog;
		});
	},
	methods: {
		async deleteEvent(id) {
			const res = await this.$axios.$post('/api/calendar/event/delete/' + id);
			if (res.deleted >= 1)
				this.$root.$emit('eventDeleted', id);
		},
		async deleteSession(eventid, sessionid) {
			const res = await this.$axios.$post('/api/calendar/eventsession/delete/' + sessionid);
			if (res.deleted >= 1)
				this.$root.$emit('eventSessionDeleted', eventid, sessionid);
		},
		createEvent() {
			this.mode = 'create';
			this.showEventDialog = !this.showEventDialog;
		},
		createEventSession(event) {
			this.activeEvent = event;
			this.mode = 'create';
			this.showSessionDialog = !this.showSessionDialog;
		},
		updateEvent(event) {
			this.activeEvent = event;
			this.mode = 'update';
			this.showEventDialog = !this.showEventDialog;
		},
		updateEventSession(event, session) {
			this.activeEvent = event;
			this.activeEventSession = session;
			this.mode = 'update';
			this.showSessionDialog = !this.showSessionDialog;
		},
		toggleSessions(id) {
			if (this.shownSessions.includes(id)) {
				let index = this.shownSessions.findIndex(e => e == id);
				this.shownSessions.splice(index, 1);
			} else {
				this.shownSessions.push(id);
			}
		},
		sessionStart(starttime) {
			return moment(starttime).format('ddd Do HH:mm')+'h';
		}
	}
};
</script>

<style lang="scss" scoped>
.headline {
	margin-bottom: 1em;
}
.sessions {
	padding-left: 2em
}
</style>
