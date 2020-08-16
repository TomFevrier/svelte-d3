<script>
	import { csv, tsv, json } from 'd3-fetch';
	import { scaleLinear, scaleLog } from 'd3-scale';
	import { nest } from 'd3-collection';
	import { format } from 'd3-format';

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
			console.log(dataScatterPlot)
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

	let template = (d) => `
		<p>${d.key}: ${d.value}</p>
	`
</script>

<main>
	<h1>Demo - Svelte &times; D3.js</h1>
	<p>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Cumque, quas delectus? Modi similique nostrum veritatis corrupti sunt dicta itaque eius maxime eveniet perferendis, expedita asperiores ipsa illo odio, impedit optio.</p>
	<p>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Culpa quod eligendi vitae, doloribus dicta pariatur nihil autem, nisi velit corporis unde delectus. Ipsam fuga nihil ipsum quod facere, eligendi consectetur!</p>
	<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Tempore dolor, provident non hic aperiam aut, amet magnam eius neque aliquid laboriosam. Placeat porro architecto est dignissimos error suscipit nesciunt fugiat.</p>
	<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Fugiat illo minima magni quia cum suscipit, alias quasi quidem inventore a. Laborum labore suscipit soluta enim alias. Inventore eos dolores vel.</p>
	<p>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Cumque, quas delectus? Modi similique nostrum veritatis corrupti sunt dicta itaque eius maxime eveniet perferendis, expedita asperiores ipsa illo odio, impedit optio.</p>
	<p>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Culpa quod eligendi vitae, doloribus dicta pariatur nihil autem, nisi velit corporis unde delectus. Ipsam fuga nihil ipsum quod facere, eligendi consectetur!</p>
	<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Tempore dolor, provident non hic aperiam aut, amet magnam eius neque aliquid laboriosam. Placeat porro architecto est dignissimos error suscipit nesciunt fugiat.</p>
	<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Fugiat illo minima magni quia cum suscipit, alias quasi quidem inventore a. Laborum labore suscipit soluta enim alias. Inventore eos dolores vel.</p>
	<p>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Cumque, quas delectus? Modi similique nostrum veritatis corrupti sunt dicta itaque eius maxime eveniet perferendis, expedita asperiores ipsa illo odio, impedit optio.</p>
	<p>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Culpa quod eligendi vitae, doloribus dicta pariatur nihil autem, nisi velit corporis unde delectus. Ipsam fuga nihil ipsum quod facere, eligendi consectetur!</p>
	<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Tempore dolor, provident non hic aperiam aut, amet magnam eius neque aliquid laboriosam. Placeat porro architecto est dignissimos error suscipit nesciunt fugiat.</p>
	<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Fugiat illo minima magni quia cum suscipit, alias quasi quidem inventore a. Laborum labore suscipit soluta enim alias. Inventore eos dolores vel.</p>
	<!-- {#if dataBarChartRace}
		<Chart
			type='BarChartRace'
			data={dataBarChartRace}
			title="Highest-valued companies (2000 - 2019)"
			width={800} height={400}
			key='key' value='value'
			limit={5}
			animate duration={2000} />
	{/if} -->
	{#if dataBarChart}
		<Chart
			type='BarChart'
			data={dataBarChart}
			title="Genres of top movies of 2018"
			width={800} height={500}
			key='key' value='value'
			barOrientation='horizontal' sortBars grid
			animate duration={2000}
			tooltip tooltipTemplate={template} />
		<Chart
			type='BarChart'
			data={dataBarChart}
			title="Genres of top movies of 2018"
			width={800} height={500}
			key='key' value='value'
			barOrientation='vertical' sortBars grid
			animate duration={2000}
			tooltip tooltipTemplate={template} />
	{/if}
	{#if dataLineChart}
		<Chart
			type='LineChart'
			data={dataLineChart}
			title="Some random values"
			width={800} height={500}
			xTicks="{{ value: 5 }}"
			yTicks="{{ value: 14, type: 'every' }}" yGrid
			animate duration={5000} />
	{/if}
	{#if dataScatterPlot}
		<Chart
			type='ScatterPlot'
			data={dataScatterPlot}
			title="Health and wealth of nations"
			width={800} height={500}
			x='gdp' xScaleType={scaleLog} xTicks="{{ value: 5 }}" xTickFormat="{ d => format('~s')(d / 1e6) }" xGrid
			y='life_expectancy' yScaleType={scaleLinear} yGrid
			r='population'
			animate duration={2000}
			tooltip tooltipTemplate={d => d.country} />
		<Chart
			type='ScatterPlot'
			data={dataScatterPlot}
			title="Health and wealth of nations"
			width={800} height={500}
			x='gdp' xScaleType={scaleLog} xTicks="{{ value: 5 }}" xTickFormat="{ d => format('~s')(d / 1e6) }" xGrid
			y='life_expectancy' yScaleType={scaleLinear} yGrid
			r={10} />
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
