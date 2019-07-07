<template>
<div class="md-layout-item md-size-20 filter">
	<h1>Filter</h1>
	<div class="list">
		<md-list :md-expand-single="expandSingle">
			<md-list-item>
				<md-autocomplete
					v-model="selectedSeries"
					:md-options="seriesArr"
					:md-fuzzy-search="false"
				>
					<label>Series</label>
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
					:md-options="tracksArr"
					:md-fuzzy-search="false"
				>
					<label>Track</label>
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
	<md-button
		class="md-raised md-primary"
		@click.native="toggleCurrentEvents()"
	>
		{{ showCurrentEventsLabel }}
	</md-button>
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
      selectedSeries: false,
      selectedTracks: false,
      seriesArr: this.series,
      tracksArr: this.tracks
    };
  },
  computed: {
    showCurrentEventsLabel: function() {
      return this.showCurrentEvents ? 'Show all' : 'Show current';
    }
  },
  methods: {
    // createEvent: function() {
    // 	this.$root.$emit('toggleCrudEvent');
    // },
    // createSeries: function() {
    // 	this.$root.$emit('toggleCrudSeries');
    // },
    // createTrack: function() {
    // 	this.$root.$emit('toggleCrudTrack');
    // },
    toggleCurrentEvents: function() {
      this.$root.$emit('toggleCurrentEvents');
    }
  }
};
</script>

<style lang="scss">
@import "~assets/scss/main.scss";

.filter {
  font-family: $default-family;
  border-width: 1px;
  border-style: solid;
  border-color: white;
  border-radius: 25px;
  padding-right: 1.5em;
  padding-left: 1.5em;

  h1 {
    font-weight: 600;
  }
  .md-list {
    background-color: transparent;
  }
}
</style>
