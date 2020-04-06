<script>
	import { onMount } from 'svelte';
	import { line } from 'd3-shape';
	import { select, selectAll } from 'd3-selection';
	import { transition } from 'd3-transition';

	export let data;
	export let x;
	export let xScale;
	export let y;
	export let yScale;
	export let animate;
	export let duration;

	let g;

	onMount(() => {
		const lineGenerator = line()
			.x(d => xScale(d[x]))
			.y(d => yScale(d[y]));

		let path = select(g).append('path');

		path.datum(data)
			.attr('d', lineGenerator)
			.attr('fill', 'none')
			.attr('stroke', 'rebeccapurple')
			.attr('stroke-width', 2)
			.attr('stroke-linecap', 'round');

		if (animate) {
			path
				.attr('stroke-dasharray', path.node().getTotalLength())
				.attr('stroke-dashoffset', path.node().getTotalLength())
				.transition()
				.duration(duration)
					.attr('stroke-dashoffset', 0);
		}
	});
</script>

<g bind:this={g} class="line-chart"></g>
