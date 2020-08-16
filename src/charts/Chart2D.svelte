<script>
	import { onMount } from 'svelte';
	import { extent } from 'd3-array';

	import Axis from '../components/Axis.svelte';
	import Grid from '../components/Grid.svelte';
	import ScatterPlot from './ScatterPlot.svelte';
	import LineChart from './LineChart.svelte';

	// General props
	export let type;
	export let data;

	// Layout props
	export let width;
	export let height;
	export let margin;

	export let x;
	export let xScaleType;
	export let xAxisOrient;
	export let xTicks;
	export let xTickFormat;
	export let xGrid;
	export let y;
	export let yScaleType;
	export let yAxisOrient;
	export let yTicks;
	export let yTickFormat;
	export let yGrid;
	export let r; // For ScatterPlot

	// Props for animation
	export let animate;
	export let duration;

	// Props for tooltip
	export let tooltipRef;
	export let tooltipTemplate;

	let svg;

	$: xScale = xScaleType()
		.domain(extent(data, d => d[x]))
		.range([margin, width - margin]);

	$: yScale = yScaleType()
		.domain(extent(data, d => d[y]))
		.range([height - margin, margin]);
</script>

<svg bind:this={svg} {width} {height} class='chart'>
	{#if type === 'ScatterPlot'}
		{#if xGrid}
			<Grid {width} {height} {margin} scale={xScale} direction='vertical' ticks={xTicks} />
		{/if}
		{#if yGrid}
			<Grid {width} {height} {margin} scale={yScale} direction='horizontal' ticks={yTicks} />
		{/if}
		<Axis {width} {height} {margin} scale={xScale} orient={xAxisOrient} ticks={xTicks} tickFormat={xTickFormat} />
		<Axis {width} {height} {margin} scale={yScale} orient={yAxisOrient} ticks={yTicks} tickFormat={yTickFormat} />
		<ScatterPlot
			{data}
			{x} {xScale}
			{y} {yScale}
			{r}
			{animate} {duration}
			{tooltipRef} {tooltipTemplate} />
	{:else if type === 'LineChart'}
		{#if xGrid}
			<Grid {width} {height} {margin} scale={xScale} direction='vertical' ticks={xTicks} />
		{/if}
		{#if yGrid}
			<Grid {width} {height} {margin} scale={yScale} direction='horizontal' ticks={yTicks} />
		{/if}
		<Axis {width} {height} {margin} scale={xScale} orient={xAxisOrient} ticks={xTicks} tickFormat={xTickFormat} />
		<Axis {width} {height} {margin} scale={yScale} orient={yAxisOrient} ticks={yTicks} tickFormat={yTickFormat} />
		<LineChart
			{data}
			{x} {xScale}
			{y} {yScale}
			{animate} {duration} />
	{/if}
	<svg:defs>
		<svg:marker
			id='arrow'
			markerWidth={30}
			markerHeight={30}
			refX={6}
			refY={6}
			orient='auto'
		>
			<path d='M 0 0 12 6 0 12 3 6' />
		</svg:marker>
	</svg:defs>
</svg>
