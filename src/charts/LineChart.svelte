<script>
	import { fade } from 'svelte/transition';
	import { line } from 'd3-shape';
	import { transition } from 'd3-transition';

	export let data;
	export let x;
	export let xScale;
	export let y;
	export let yScale;
	export let animate;
	export let duration;

	let g;
	let scrollY;

	$: visible = animate && g && scrollY > g.parentNode.getBoundingClientRect().y + window.innerHeight * 0.5;

	$: lineGenerator = line()
		.x(d => xScale(d[x]))
		.y(d => yScale(d[y]));

	const reveal = (node) => {
		if (!animate) return;
		const length = node.getTotalLength();
		node.style.strokeDasharray = length;
		return {
			duration,
			css: (t, u) => `stroke-dashoffset: ${u * length}`
		};
	}
</script>

<svelte:window bind:scrollY />
<g bind:this={g} class='line-chart'>
	{#if !animate || visible}
		<path
			d={lineGenerator(data)}
			fill='none'
			stroke='rebeccapurple'
			stroke-width={2}
			stroke-linecap='round'
			in:reveal
			out:fade
		/>
	{/if}
</g>
