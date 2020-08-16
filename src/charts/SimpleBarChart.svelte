<script>
	import { fade } from 'svelte/transition';
	import { select } from 'd3-selection';
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
	export let tooltipRef;
	export let tooltipTemplate;

	let g;
	let scrollY;

	$: visible = animate && g && scrollY > g.parentNode.getBoundingClientRect().y + window.innerHeight * 0.5;

	$: select(tooltipRef).style('transform', barOrientation === 'vertical'
		? 'translate(-50%, -120%)'
		: 'translate(20%, -50%)');

	const reveal = (node) => {
		if (!animate) return;
		if (barOrientation === 'vertical') {
			const y = select(node).attr('y');
			const height = select(node).attr('height');
			return {
				duration,
				tick: (t, u) => {
					select(node)
						.attr('y', u * scale(0) + t * y)
						.attr('height', t * height)
				}
			};
		}
		const width = select(node).attr('width');
		return {
			duration,
			tick: t => select(node).attr('width', t * width)
		};
	}

	const showTooltip = (d) => {
		if (!tooltipRef) return;
		select(tooltipRef)
			.html(tooltipTemplate(d))
			.style('left', barOrientation === 'vertical'
				? `${labels(d[key]) + labels.bandwidth() / 2}px`
				: `${scale(d[value])}px`)
			.style('top', barOrientation == 'vertical'
				? `${scale(d[value])}px`
				: `${labels(d[key]) + labels.bandwidth() / 2}px`)
			.style('opacity', 1);
	}

	const hideTooltip = () => {
		if (!tooltipRef) return;
		select(tooltipRef).style('opacity', 0);
	}
</script>

<svelte:window bind:scrollY />
<g bind:this={g} class='bar-chart'>
	{#if !animate || visible}
		{#each data as d}
			<rect
				x={barOrientation === 'vertical' ? labels(d[key]) : scale(0)}
				y={barOrientation === 'vertical' ? scale(d[value]) : labels(d[key])}
				width={barOrientation === 'vertical'
					? labels.bandwidth()
					: labels(d[key])
				}
				height={barOrientation === 'vertical'
					? scale(0) - scale(d[value])
					: labels.bandwidth()
				}
				fill='rebeccapurple'
				in:reveal|once
				out:fade
				on:mouseover={() => showTooltip(d)}
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
