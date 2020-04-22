<script>
	import { max } from 'd3-array';
	import { scaleBand, scaleLinear } from 'd3-scale';

	import Axis from '../components/Axis.svelte';
	import Grid from '../components/Grid.svelte';
	import SimpleBarChart from './SimpleBarChart.svelte';

	// General props
	export let type;
	export let data;

	// Layout props
	export let width;
	export let height;
	export let margin;

	export let key;
	export let value;
	export let barOrientation;
	export let sortBars;
	export let grid;

	// Props for animation
	export let animate;
	export let duration;

	if (sortBars)
		data = data.sort((a, b) => b[value] - a[value]);

	const labels = scaleBand()
		.domain(data.map(e => e[key]))
		.range(barOrientation === 'vertical'
			? [margin, width - margin]
			: [margin, height - margin])
		.paddingInner(0.1);

	const scale = scaleLinear()
		.domain([0, max(data, d => d[value])])
		.range(barOrientation === 'vertical'
			? [height - margin, margin]
			: [margin, width - margin]);
</script>

<svg {width} {height} class="chart">
	{#if type === 'BarChart'}
		{#if grid}
			<Grid
				{width} {height} {margin}
				scale={scale}
				direction="{barOrientation === 'vertical' ? 'horizontal' : 'vertical'}" />
		{/if}
		<Axis
			{width} {height} {margin} scale={labels}
			orient="{barOrientation === 'vertical' ? 'bottom' : 'left'}"
			hideArrow hideTicks />
		<Axis
			{width} {height} {margin} scale={scale}
			orient="{barOrientation === 'vertical' ? 'left' : 'bottom'}" />
		<SimpleBarChart
			{data}
			{key} {labels}
			{value} {scale}
			{barOrientation}
			{animate} {duration} />
	{/if}
</svg>
