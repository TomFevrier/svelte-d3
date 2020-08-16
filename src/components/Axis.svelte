<script>
	import { onMount, beforeUpdate } from 'svelte';
	import { select } from 'd3-selection';
	import { axisBottom, axisTop, axisLeft, axisRight } from 'd3-axis';
	import { range } from 'd3-array';

	export let width;
	export let height;
	export let margin;
	export let scale;
	export let orient = 'bottom';
	export let ticks = null;
	export let tickFormat = null;
	export let hideArrow = false;
	export let hideTicks = false;

	let g;

	let axis;
	let transform;

	$: {
		select(g).selectAll('*').remove();
		switch(orient) {
			case 'bottom':
				axis = axisBottom(scale).tickSizeOuter(0);
				transform = `translate(0, ${height - margin})`;
				break;
			case 'top':
				axis = axisTop(scale).tickSizeOuter(0);
				transform = `translate(0, ${margin})`;
				break;
			case 'left':
				axis = axisLeft(scale).tickSizeOuter(0);
				transform = `translate(${margin}, 0)`;
				break;
			case 'right':
				axis = axisRight(scale).tickSizeOuter(0);
				transform = `translate(${width - margin}, 0)`;
		}
		if (ticks) {
			if (ticks.type == 'every')
				axis.tickValues(range(scale.domain()[0], scale.domain()[1] + 1, ticks.value));
			else
				axis.ticks(ticks.value);
		}
		if (tickFormat) axis.tickFormat(tickFormat);
		if (hideTicks) axis.tickSize(0);
		select(g).call(axis);
		if (!hideArrow) {
			select(g).select('path').attr('marker-end', 'url(#arrow)');
		}
	}
</script>

<g bind:this={g} class='axis' {transform}></g>
