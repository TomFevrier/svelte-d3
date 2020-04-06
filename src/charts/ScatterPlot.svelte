<script>
	import { onMount } from 'svelte';
	import { select, selectAll } from 'd3-selection';
	import { scaleSqrt } from 'd3-scale';
	import { extent } from 'd3-array';
	import { transition } from 'd3-transition';

	export let data;
	export let x;
	export let xScale;
	export let y;
	export let yScale;
	export let r;
	export let animate;
	export let duration;

	let g;

	onMount(() => {
		if (typeof r === 'string') {
			var radiusScale = scaleSqrt()
				.domain(extent(data, d => d[r]))
				.range([2, 50]);
		}

		let circles = select(g).selectAll('circle')
			.data(data).enter()
			.append('circle')
				.attr('cx', d => xScale(d[x]))
				.attr('cy', d => yScale(d[y]))
				.attr('fill', 'rebeccapurple');

		if (animate) {
			circles = circles
				.attr('r', 0)
				.transition()
				.duration(duration)
		}

		circles.attr('r', typeof r === 'string'
			? d => radiusScale(d[r])
			: r
		);
	});
</script>

<g bind:this={g} class="scatter-plot"></g>
