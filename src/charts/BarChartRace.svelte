<script>
	import { onMount } from 'svelte';
	import { select, selectAll } from 'd3-selection';
	import { scaleLinear, scaleBand } from 'd3-scale';
	import { min, max, range } from 'd3-array';
	import { transition } from 'd3-transition';
	import { axisTop } from 'd3-axis';
	import { interpolate } from 'd3-interpolate';
	import { easeLinear } from 'd3-ease';

	import Axis from '../components/Axis.svelte';

	export let data;
	export let margin;
	export let width;
	export let height;
	export let limit;
	export let animate;
	export let duration;

	let g;

	const labels = scaleBand()
		.domain(range(limit + 1))
		.range([margin, margin + limit / (limit - 1) * (height - 2 * margin)])
		.paddingInner(0.1);

	console.log(data[0])

	let scale = scaleLinear()
		.domain([0, max(data[0].value, d => d.value)])
		.range([margin, width - margin]);

	onMount(() => {
		const onEnter = (enter) => {
			const bar = enter.append('g')
				.attr('class', 'bar')
				.attr('transform', `translate(${scale(0)}, ${labels(limit)})`)
				.style('opacity', 0);

			bar.append('rect')
				.attr('x', 0)
				.attr('y', 0)
				.attr('width', d => scale(d.value) - scale(0))
				.attr('height', labels.bandwidth())
				.attr('fill', 'rebeccapurple');

			bar.append('text')
				.attr('transform', `translate(5, ${labels.bandwidth() / 2 + 5})`)
				.attr('fill', 'white')
				.property('_current', d => {
					if (new Date(d.date).getFullYear() == min(data, d => +d.key))
						return 0;
					return data.find(e => e.key == new Date(d.date).getFullYear() - 1).value
						.find(e => e.name == d.name)
						.value;
				});

			bar.transition()
				.ease(easeLinear)
				.duration(duration)
				.attr('transform', (d, i) => `translate(${scale(0)}, ${labels(i)})`)
				.style('opacity', 1);

			bar.select('text')
				.transition()
				.duration(duration)
				.textTween(function(d) {
					const i = interpolate(this._current, d.value);
					this._current = d.value;
					return (t) => `${d.name}: ${Math.round(i(t))} million dollars`;
				});
		}

		const onUpdate = (update) => {
			update
				.transition()
				.ease(easeLinear)
				.duration(duration)
					.attr('transform', (d, i) => `translate(${scale(0)}, ${labels(i)})`);

			update.select('rect')
				.transition()
				.ease(easeLinear)
				.duration(duration)
					.attr('width', d => scale(d.value) - scale(0));

			update.select('text')
				.transition()
				.ease(easeLinear)
				.duration(duration)
				.textTween(function(d) {
					const i = interpolate(this._current, d.value);
					this._current = d.value;
					return (t) => `${d.name}: ${Math.round(i(t))} million dollars`;
				});
		}

		const onExit = (exit) => {
			exit.transition()
				.ease(easeLinear)
				.duration(duration)
					.attr('transform', `translate(${scale(0)}, ${labels(limit)})`)
					.style('opacity', 0)
					.remove();
		}

		const animate = (index) => {
			scale.domain([0, max(data[index].value, d => d.value)]);

			select(g).selectAll('.bar')
				.data(data[index].value.splice(0, limit), d => d.name)
				.join(onEnter, onUpdate, onExit);

			select(g.parentNode).select('.axis')
				.transition()
				.duration(duration)
				.call(axisTop(scale));

			select(g).select('.legend')
				.text(data[index].key)
				.attr('x', scale(scale.domain()[1]))
				.attr('y', labels(limit - 1) + labels.bandwidth() / 2);

			if (index + 1 < data.length)
				setTimeout(() => animate(index + 1), duration);
		}

		animate(0);

	});
</script>

<g bind:this={g} class="bar-chart-race">
	<text class='legend' />
</g>
<Axis {width} {height} {margin} bind:scale={scale} orient='top' hideArrow />

<style>
	.legend {
		font-size: 42px;
		font-weight: bold;
		text-anchor: end;
		z-index: 42;
	}
</style>
