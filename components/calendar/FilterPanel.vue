<template>
<div class="md-layout-item md-size-20 filter">
	<h1>Filter</h1>
	<div v-if="seriesChips.length !== 0 || tracksChips.length !== 0" class="chips">
		<h2>Active Filters</h2>
		<div class="serieChipContainer">
			<h3 v-if="seriesChips.length !== 0">
				Series
			</h3>
			<md-chip
				v-for="(serie, index) in seriesChips"
				:key="serie.id"
				md-deletable
				@md-delete="removeSerieChip(serie, index)"
			>
				{{ serie.name }}
			</md-chip>
		</div>
		<div class="trackChipContainer">
			<h3 v-if="tracksChips.length !== 0">
				Tracks
			</h3>
			<md-chip
				v-for="(track, index) in tracksChips"
				:key="track.id"
				md-deletable
				@md-delete="removeTrackChip(track, index)"
			>
				{{ track.name }}
			</md-chip>
		</div>
	</div>

	<div class="list">
		<md-list :md-expand-single="expandSingle">
			<md-list-item>
				<md-autocomplete
					v-model="selectedSeries"
					:md-options="seriesArr.map(x=>({'id': x.id, 'name': x.name, 'toLowerCase': () => x.name.toLowerCase(), 'toString':() => x.name}))"
					:md-fuzzy-search="false"
				>
					<label class="autoLabel">Series</label>
					<md-icon>keyboard_arrow_down</md-icon>
					<template slot="md-autocomplete-item" slot-scope="{ item, term }">
						<span class="color" :style="`background-color: ${item.color}`" />
						<md-highlight-text
							:md-term="typeof term === 'object' && term.name ? term.name : ''"
						>
							{{ item.name }}
						</md-highlight-text>
					</template>

					<template slot="md-autocomplete-empty" slot-scope="{ term }">
						"{{ term }}" not found!
					</template>
				</md-autocomplete>
			</md-list-item>
			<md-list-item>
				<md-autocomplete
					v-model="selectedTracks"
					:md-options="tracksArr.map(x=>({'id': x.id, 'name': x.name, 'toLowerCase': () => x.name.toLowerCase(), 'toString':() => x.name}))"
					:md-fuzzy-search="false"
				>
					<label class="autoLabel">Track</label>
					<md-icon>keyboard_arrow_down</md-icon>
					<template slot="md-autocomplete-item" slot-scope="{ item, term }">
						<span class="color" :style="`background-color: ${item.color}`" />
						<md-highlight-text
							:md-term="typeof term === 'object' && term.name ? term.name : ''"
						>
							{{ item.name }}
						</md-highlight-text>
					</template>

					<template slot="md-autocomplete-empty" slot-scope="{ term }">
						"{{ term }}" not found!
					</template>
				</md-autocomplete>
			</md-list-item>
		</md-list>
	</div>
	<!--<md-button
		class="md-raised md-primary"
		@click.native="toggleCurrentEvents()"
	>
		{{ showCurrentEventsLabel }}
    </md-button>-->
</div>
</template>

<script>
export default {
  props: {
    showCurrentEvents: {
      type: Boolean,
      default: true
    },
    series: {
      type: Array,
      default() {
        return [];
      }
    },
    tracks: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  data() {
    return {
      expandSingle: false,
      selectedSeries: '',
      selectedTracks: '',
      seriesArr: this.series,
      tracksArr: this.tracks,
      seriesChips: [],
      tracksChips: []
    };
  },
  computed: {
    showCurrentEventsLabel: function() {
      return this.showCurrentEvents ? 'Show all' : 'Show current';
    }
  },
  watch: {
    selectedSeries: function(val) {
      if (val !== undefined && val.name && val.name.length) {
        this.series.forEach(entry => {
          if (entry.name === val.name) {
            this.seriesChips.push(val);
            let index = this.seriesArr.findIndex(serie => serie.id == val.id);
            this.seriesArr.splice(index, 1);
            this.selectedSeries = {
              id: '',
              name: '',
              toLowerCase: () => '',
              toString: () => ''
            };
          }
        });
        this.filterCalendar();
      }
    },
    selectedTracks: function(val) {
      if (val !== undefined && val.name && val.name.length) {
        this.tracks.forEach(entry => {
          if (entry.name === val.name) {
            this.tracksChips.push(val);
            let index = this.tracksArr.findIndex(track => track.id == val.id);
            this.tracksArr.splice(index, 1);
            this.selectedTracks = {
              id: '',
              name: '',
              toLowerCase: () => '',
              toString: () => ''
            };
          }
        });
        this.filterCalendar();
      }
    }
  },
  methods: {
    // toggleCurrentEvents: function() {
    //   this.$root.$emit('toggleCurrentEvents');
    // },
    removeSerieChip: function(serie, index) {
      this.seriesChips.splice(index, 1);
      this.seriesArr.push(serie);
      this.filterCalendar();
    },
    removeTrackChip: function(track, index) {
      this.tracksChips.splice(index, 1);
      this.tracksArr.push(track);
      this.filterCalendar();
    },
    filterCalendar: function() {
      this.$root.$emit('filterCalendar', {
        series: this.seriesChips,
        tracks: this.tracksChips
      });
    }
  }
};
</script>

