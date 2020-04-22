<script>
	import { scaleLinear } from 'd3-scale';

	import Chart2D from './charts/Chart2D.svelte';
	import BarChart from './charts/BarChart.svelte';
	import BarChartRace from './charts/BarChartRace.svelte';

	// General props
	export let type;
	export let data;

	// Layout props
	export let title;
	export let width;
	export let height;
	export let margin = 80;

	// Props for XY chart (ScatterPlot, LineChart...)
	export let x = 'x';
	export let xScaleType = scaleLinear;
	export let xAxisOrient = 'bottom';
	export let xTicks = null;
	export let xTickFormat = null;
	export let xGrid = false;
	export let y = 'y';
	export let yScaleType = scaleLinear;
	export let yAxisOrient = 'left';
	export let yTicks = null;
	export let yTickFormat = null;
	export let yGrid = false;
	export let r = 10; // For ScatterPlot

	// Props for BarChart
	export let key = 'key';
	export let value = 'value';
	export let barOrientation = 'vertical';
	export let sortBars = false;
	export let grid = false;

	// Props for BarChartRace
	export let limit = 10;

	// Props for animation
	export let animate = false;
	export let duration = 1000;
</script>

<h2 class="chart-title">{title}</h2>
{#if type === 'LineChart' || type === 'ScatterPlot'}
	<Chart2D
	 	{type}
		{data}
		{width} {height} {margin}
		{x} {xScaleType} {xAxisOrient} {xTicks} {xTickFormat} {xGrid}
		{y} {yScaleType} {yAxisOrient} {yTicks} {yTickFormat} {yGrid}
		{r}
		{animate} {duration} />
{:else if type === 'BarChart'}
	<BarChart
	 	{type}
		{data}
		{width} {height} {margin}
		{key} {value}
		{barOrientation}
		{sortBars}
		{grid}
		{animate} {duration} />
{:else if type === 'BarChartRace'}
	<BarChartRace
		{data}
		{margin} {width} {height}
		{limit}
		{animate} {duration} />
{/if}
