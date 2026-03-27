<svelte:options runes={false} />

<script>
	import * as d3 from 'd3';

	export let data = [];

	const width = 420;
	const height = 420;
	const radius = 130;

	let hovered = null;

	$: salesByGenre = Array.from(
		d3.rollup(
			data,
			(values) => d3.sum(values, (d) => d.globalSales),
			(d) => d.genre
		),
		([genre, sales]) => ({ genre, sales })
	);

	$: color = d3
		.scaleOrdinal()
		.domain(salesByGenre.map((d) => d.genre))
		.range(d3.schemeTableau10);

	$: pieGenerator = d3.pie().value((d) => d.sales).sort(null);
	$: pieData = pieGenerator(salesByGenre);

	$: arcGenerator = d3.arc().innerRadius(0).outerRadius(radius);
</script>

<div class="chart-card">
	<h3>Pie Chart</h3>
	<p>Verteilung der globalen Verkäufe nach Genre.</p>

	<div class="chart-layout">
		<svg {width} {height} viewBox={`0 0 ${width} ${height}`}>
			<g transform={`translate(${width / 2}, ${height / 2})`}>
				{#each pieData as arcPart}
					<path
						d={arcGenerator(arcPart)}
						fill={color(arcPart.data.genre)}
						stroke="#0b1020"
						stroke-width="3"
						opacity={hovered?.genre === arcPart.data.genre ? 1 : 0.88}
						transform={hovered?.genre === arcPart.data.genre ? 'scale(1.06)' : 'scale(1)'}
						role="presentation"
						aria-hidden="true"
						style="transition: all 0.25s ease;"
						on:mouseenter={() => (hovered = arcPart.data)}
						on:mouseleave={() => (hovered = null)}
					/>
				{/each}
			</g>
		</svg>

		<div class="legend">
			<h4>Genre-Legende</h4>
			{#each salesByGenre as item}
				<div class="legend-item">
					<span class="legend-color" style={`background:${color(item.genre)}`}></span>
					<span>{item.genre}: {item.sales.toFixed(1)} Mio.</span>
				</div>
			{/each}
		</div>
	</div>

	{#if hovered}
		<div class="tooltip-box">
			<strong>{hovered.genre}</strong><br />
			Global Sales: {hovered.sales.toFixed(1)} Mio.
		</div>
	{/if}
</div>