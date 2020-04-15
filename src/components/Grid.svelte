<script>
	import { onMount } from 'svelte';
	import { select } from 'd3-selection';
	import { axisBottom, axisTop, axisLeft, axisRight } from 'd3-axis';
	import { range } from 'd3-array';

	export let width;
	export let height;
	export let margin;
	export let scale;
	export let direction = 'horizontal';
	export let ticks = null;

	let g;
	let grid;

	onMount(() => {
		if (direction == 'horizontal') {
			grid = axisLeft(scale)
				.tickSize(2 * margin - width);
			select(g).attr('transform', `translate(${margin}, 0)`);
		}
		else {
			grid = axisBottom(scale)
				.tickSize(2 * margin - height);
			select(g).attr('transform', `translate(0, ${height - margin})`);
		}

		if (ticks) {
			if (ticks.type == 'every')
				grid.tickValues(range(scale.domain()[0], scale.domain()[1] + 1, ticks.value));
			else
				grid.ticks(ticks.value);
		}

		grid.tickSizeOuter(0).tickFormat("");

		select(g).call(grid);
	});
</script>

<g bind:this={g} class="grid"></g>

<style>
	.grid {
		color: lightgrey;
	}
</style>
