<svelte:options runes={false} />

<script>
	import * as d3 from 'd3';

	export let data = [];

	const width = 760;
	const height = 430;
	const margin = { top: 30, right: 180, bottom: 60, left: 70 };

	const innerWidth = width - margin.left - margin.right;
	const innerHeight = height - margin.top - margin.bottom;

	let hovered = null;

	$: genres = [...new Set(data.map((d) => d.genre))];

	$: color = d3
		.scaleOrdinal()
		.domain(genres)
		.range(d3.schemeTableau10);

	$: xScale = d3
		.scaleLinear()
		.domain([d3.min(data, (d) => d.year) - 1, d3.max(data, (d) => d.year) + 1])
		.range([0, innerWidth]);

	$: yScale = d3
		.scaleLinear()
		.domain([0, d3.max(data, (d) => d.globalSales) * 1.15])
		.range([innerHeight, 0]);

	$: xTicks = xScale.ticks(6).map((d) => Math.round(d));
	$: yTicks = yScale.ticks(5);
</script>

<div class="chart-card chart-wide">
	<h3>Scatterplot</h3>
	<p>Zusammenhang zwischen Erscheinungsjahr und globalen Verkaufszahlen.</p>

	<div class="scatter-layout">
		<svg {width} {height} viewBox={`0 0 ${width} ${height}`}>
			<g transform={`translate(${margin.left}, ${margin.top})`}>
				{#each yTicks as tick}
					<line
						x1="0"
						x2={innerWidth}
						y1={yScale(tick)}
						y2={yScale(tick)}
						stroke="rgba(255,255,255,0.08)"
					/>
				{/each}

				{#each xTicks as tick}
					<line
						y1="0"
						y2={innerHeight}
						x1={xScale(tick)}
						x2={xScale(tick)}
						stroke="rgba(255,255,255,0.05)"
					/>
				{/each}

				<line
					x1="0"
					x2={innerWidth}
					y1={innerHeight}
					y2={innerHeight}
					stroke="#7dd3fc"
					stroke-opacity="0.6"
				/>
				<line x1="0" x2="0" y1="0" y2={innerHeight} stroke="#7dd3fc" stroke-opacity="0.6" />

				{#each xTicks as tick}
					<g transform={`translate(${xScale(tick)}, ${innerHeight})`}>
						<line y2="6" stroke="#93c5fd" />
						<text y="22" text-anchor="middle" font-size="12" fill="#dbeafe">
							{tick}
						</text>
					</g>
				{/each}

				{#each yTicks as tick}
					<g transform={`translate(0, ${yScale(tick)})`}>
						<line x2="-6" stroke="#93c5fd" />
						<text x="-10" y="4" text-anchor="end" font-size="12" fill="#dbeafe">
							{tick}
						</text>
					</g>
				{/each}

				<text
					x={innerWidth / 2}
					y={innerHeight + 48}
					text-anchor="middle"
					font-size="13"
					fill="#ffffff"
				>
					Erscheinungsjahr
				</text>

				<text
					transform={`translate(-50, ${innerHeight / 2}) rotate(-90)`}
					text-anchor="middle"
					font-size="13"
					fill="#ffffff"
				>
					Global Sales (Mio.)
				</text>

				{#each data as d}
					<circle
						cx={xScale(d.year)}
						cy={yScale(d.globalSales)}
						r={hovered?.title === d.title ? 9 : 6.5}
						fill={color(d.genre)}
						opacity={hovered?.title === d.title ? 1 : 0.88}
						stroke="#0b1020"
						stroke-width={hovered?.title === d.title ? 3 : 1.5}
						role="presentation"
						aria-hidden="true"
						style="transition: all 0.2s ease;"
						on:mouseenter={() => (hovered = d)}
						on:mouseleave={() => (hovered = null)}
					/>
				{/each}

				<g transform={`translate(${innerWidth + 20}, 16)`}>
					<text x="0" y="-6" font-size="13" font-weight="700" fill="#ffffff">Legende</text>
					{#each genres as genre, i}
						<g transform={`translate(0, ${i * 24})`}>
							<rect width="14" height="14" rx="4" fill={color(genre)} />
							<text x="22" y="11" font-size="12" fill="#dbeafe">{genre}</text>
						</g>
					{/each}
				</g>
			</g>
		</svg>
	</div>

	{#if hovered}
		<div class="tooltip-box">
			<strong>{hovered.title}</strong><br />
			Genre: {hovered.genre}<br />
			Platform: {hovered.platform}<br />
			Year: {hovered.year}<br />
			Global Sales: {hovered.globalSales} Mio.
		</div>
	{/if}
</div>