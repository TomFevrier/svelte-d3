<script>
	import { onMount } from 'svelte';
	import { select, selectAll } from 'd3-selection';
	import { scaleBand } from 'd3-scale';
	import { transition } from 'd3-transition';

	export let data;
	export let key;
	export let labels;
	export let value;
	export let scale;
	export let barOrientation;
	export let animate;
	export let duration;

	let g;

	onMount(() => {
		let bars = select(g).selectAll('rect')
			.data(data).enter()
			.append('rect')
				.attr('x', d => barOrientation === 'vertical' ? labels(d[key]) : scale(0))
				.attr('fill', 'rebeccapurple');

		if (animate) {
			bars = bars
				.attr('y', d => barOrientation === 'vertical' ? scale(0) : labels(d[key]))
				.attr('width', barOrientation === 'vertical' ? labels.bandwidth() : 0)
				.attr('height', barOrientation === 'vertical' ? 0 : labels.bandwidth())
				.transition()
				.duration(duration);
		}

		bars
			.attr('y', d => barOrientation === 'vertical' ? scale(d[value]) : labels(d[key]))
			.attr('height', barOrientation === 'vertical' ? d => scale(0) - scale(d[value]) : labels.bandwidth())
			.attr('width', barOrientation === 'vertical' ? labels.bandwidth() : d => scale(d[value]) - scale(0));
	});
</script>

<g bind:this={g} class="bar-chart"></g>
