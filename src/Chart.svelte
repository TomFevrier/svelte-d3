<script>
	import { extent, max } from 'd3-array';
	import { scaleLinear, scaleBand } from 'd3-scale';

	import Axis from './components/Axis.svelte';
	import ScatterPlot from './charts/ScatterPlot.svelte';
	import LineChart from './charts/LineChart.svelte';
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
	export let xAxisOrient = 'bottom'
	export let y = 'y';
	export let yScaleType = scaleLinear;
	export let yAxisOrient = 'left';
	export let r = 10; // For ScatterPlot

	// Props for BarChart
	export let key = 'key';
	export let value = 'value';
	export let barOrientation = 'vertical';
	export let sortBars = false;

	// Props for BarChartRace
	export let limit = 10;

	// Props for animation
	export let animate = false;
	export let duration = 1000;

	console.log(data);

	// Scales definition for XY chart
	const xScale = xScaleType()
		.domain(extent(data, d => d[x]))
		.range([margin, width - margin]);

	const yScale = yScaleType()
		.domain(extent(data, d => d[y]))
		.range([height - margin, margin]);

	// Sorting and scales definition for BarChart
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

<h2 class="chart-title">{title}</h2>
<svg {width} {height} class="chart">
	{#if type === 'ScatterPlot'}
		<ScatterPlot
			{data}
			{x} {xScale}
			{y} {yScale}
			{r}
			{animate} {duration} />
		<Axis {width} {height} {margin} scale={xScale} orient={xAxisOrient} />
		<Axis {width} {height} {margin} scale={yScale} orient={yAxisOrient} />
	{:else if type === 'LineChart'}
		<LineChart
			{data}
			{x} {xScale}
			{y} {yScale}
			{animate} {duration} />
		<Axis {width} {height} {margin} scale={xScale} orient={xAxisOrient} />
		<Axis {width} {height} {margin} scale={yScale} orient={yAxisOrient} />
	{:else if type === 'BarChart'}
		<BarChart
			{data}
			{key} {labels}
			{value} {scale}
			{barOrientation}
			{animate} {duration} />
		<Axis
			{width} {height} {margin} scale={labels}
			orient="{barOrientation === 'vertical' ? 'bottom' : 'left'}" />
		<Axis
			{width} {height} {margin} scale={scale}
			orient="{barOrientation === 'vertical' ? 'left' : 'bottom'}" />
	{:else if type === 'BarChartRace'}
		<BarChartRace
			{data}
			{margin} {width} {height}
			{limit}
			{animate} {duration} />
	{/if}
</svg>
