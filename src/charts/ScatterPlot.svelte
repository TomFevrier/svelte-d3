<script>
	import { fade } from 'svelte/transition';
	import { select } from 'd3-selection';
	import { scaleSqrt } from 'd3-scale';
	import { extent } from 'd3-array';

	export let data;
	export let x;
	export let xScale;
	export let y;
	export let yScale;
	export let r;
	export let animate;
	export let duration;
	export let tooltipRef;
	export let tooltipTemplate;

	let g;
	let radiusScale;
	let scrollY;

	$: visible = animate && g && scrollY > g.parentNode.getBoundingClientRect().y + window.innerHeight * 0.5;

	$: select(tooltipRef).style('transform', 'translate(-50%, -120%)');

	const reveal = (node) => {
		if (!animate) return;
		const radius = select(node).attr('r');
		return {
			duration,
			tick: t => select(node).attr('r', t * radius)
		};
	}

	if (typeof r === 'string') {
		radiusScale = scaleSqrt()
			.domain(extent(data, d => d[r]))
			.range([2, 50]);
	}

	const showTooltip = (d) => {
		if (!tooltipRef) return;
		select(tooltipRef)
			.html(tooltipTemplate(d))
			.style('left', `${xScale(d[x])}px`)
			.style('top', `${yScale(d[y])}px`)
			.style('opacity', 1);
	}

	const hideTooltip = () => {
		if (!tooltipRef) return;
		select(tooltipRef).style('opacity', 0);
	}
</script>

<svelte:window bind:scrollY />
<g bind:this={g} class='scatter-plot'>
	{#if !animate || visible}
		{#each data as d}
			<circle
				cx={xScale(d[x])}
				cy={yScale(d[y])}
				r={typeof r === 'string' ? radiusScale(d[r]) : r}
				fill='rebeccapurple'
				in:reveal
				out:fade
				on:mouseenter={() => showTooltip(d)}
				on:mouseleave={hideTooltip}
				class:hoverable={tooltipRef}
			/>
		{/each}
	{/if}
</g>

<style>
	.hoverable {
		cursor: pointer;
	}
</style>
