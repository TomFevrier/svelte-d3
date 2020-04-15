<script>
	import { onMount } from 'svelte';
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



	// this.svg.append('svg:defs').append('svg:marker')
	//     .attr('id', 'triangleBlue')
	//     .attr('refX', 6)
	//     .attr('refY', 6)
	//     .attr('markerWidth', 30)
	//     .attr('markerHeight', 30)
	//     .attr('markerUnits','userSpaceOnUse')
	//     .attr('orient', 'auto')
	//     .append('path')
	// 	    .attr('d', 'M 0 0 12 6 0 12 3 6')
	// 	    .attr('fill', this.colorAudience);

	onMount(() => {
		select(g.parentNode)
			.append('svg:defs').append('svg:marker')
				.attr('id', 'arrow')
				.attr('markerWidth', 30)
				.attr('markerHeight', 30)
				.attr('refX', 6)
				.attr('refY', 6)
				.attr('orient', 'auto')
					.append('path')
						.attr('d', 'M 0 0 12 6 0 12 3 6');

		switch(orient) {
			case 'bottom':
				axis = axisBottom(scale);
				select(g).attr('transform', `translate(0, ${height - margin})`);
				break;
			case 'top':
				axis = axisTop(scale);
				select(g).attr('transform', `translate(0, ${margin})`);
				break;
			case 'left':
				axis = axisLeft(scale);
				select(g).attr('transform', `translate(${margin}, 0)`);
				break;
			case 'right':
				axis = axisRight(scale);
				select(g).attr('transform', `translate(${width - margin}, 0)`);
		}

		if (ticks) {
			if (ticks.type == 'every')
				axis.tickValues(range(scale.domain()[0], scale.domain()[1] + 1, ticks.value));
			else
				axis.ticks(ticks.value);
		}

		if (tickFormat)
			axis.tickFormat(tickFormat);

		axis.tickSizeOuter(0);

		if (hideTicks)
			axis.tickSize(0);

		select(g).call(axis);

		if (!hideArrow) {
			select(g).select('path')
				.attr('marker-end', 'url(#arrow)');
		}
	});
</script>

<g bind:this={g} class="axis"></g>
