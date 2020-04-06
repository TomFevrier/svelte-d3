<script>
	import { onMount } from 'svelte';
	import { select } from 'd3-selection';
	import { axisBottom, axisTop, axisLeft, axisRight } from 'd3-axis';

	export let width;
	export let height;
	export let margin;
	export let scale;
	export let orient = 'bottom';

	let g;

	onMount(() => {
		switch(orient) {
			case 'bottom':
				select(g)
					.attr('transform', `translate(0, ${height - margin})`)
					.call(axisBottom(scale));
				break;
			case 'top':
				select(g)
					.attr('transform', `translate(0, ${margin})`)
					.call(axisTop(scale));
				break;
			case 'left':
				select(g)
					.attr('transform', `translate(${margin}, 0)`)
					.call(axisLeft(scale));
				break;
			case 'right':
				select(g)
					.attr('transform', `translate(${width - margin}, 0)`)
					.call(axisRight(scale));
		}
	});
</script>

<g bind:this={g} class="axis"></g>
