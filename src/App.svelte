<script>
	import { csv, tsv, json } from 'd3-fetch';
	import { scaleLinear, scaleLog } from 'd3-scale';
	import { nest } from 'd3-collection';

	import Chart from './Chart.svelte';

	let dataScatterPlot;
	csv('./banque_mondiale.csv')
		.then(d => {
			d.forEach(e => {
				e.gdp = +e.gdp;
				e.life_expectancy = +e.life_expectancy;
				e.population = +e.population;
			});
			dataScatterPlot = d;
		});

	const dataLineChart = Array.from({ length: 100 }, (e, i) => {
		return {
			x: i,
			y: Math.floor(Math.random() * 101)
		};
	});

	let dataBarChart;
	tsv('./top_films_2018.tsv')
		.then(d => {
			dataBarChart = nest()
				.key(d => d.genre)
				.rollup(v => v.length)
				.entries(d);
		});

	let dataBarChartRace;
	json('./barChartRace.json')
		.then(d => {
			dataBarChartRace = nest()
				.key(d => new Date(d.date).getFullYear())
				.rollup(v => v.sort((a, b) => b.value - a.value))
				.entries(d);
			console.log(dataBarChartRace)
		});
</script>

<main>
	<h1>Demo - Svelte &times; D3.js</h1>
	{#if dataBarChartRace}
		<Chart
			type='BarChartRace'
			data={dataBarChartRace}
			title="Highest-valued companies (2000 - 2019)"
			width={800} height={400}
			key='key' value='value'
			limit={5}
			animate duration={2000} />
	{/if}
	{#if dataBarChart}
		<Chart
			type='BarChart'
			data={dataBarChart}
			title="Genres of top movies of 2018"
			width={800} height={500}
			key='key' value='value'
			barOrientation='horizontal' sortBars
			animate duration={2000} />
	{/if}
	{#if dataLineChart}
		<Chart
			type='LineChart'
			data={dataLineChart}
			title="Some random values"
			width={800} height={500}
			animate duration={5000} />
	{/if}
	{#if dataScatterPlot}
		<Chart
			type='ScatterPlot'
			data={dataScatterPlot}
			title="Health and wealth of nations"
			width={800} height={500}
			x='gdp' xScaleType={scaleLog}
			y='life_expectancy' yScaleType={scaleLinear}
			r='population'
			animate duration={2000} />
	{/if}
</main>

<style lang='scss'>
	@import './global.scss';

	main {
		text-align: center;
		padding: 1em;
		width: 75%;
		margin: 0 auto;

		@include md {
			width: 100%;
		}
	}
</style>
