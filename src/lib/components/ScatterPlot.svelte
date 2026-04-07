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
	let selected = null;

	$: genres = [...new Set(data.map((d) => d.genre))];

	$: color = d3
		.scaleOrdinal()
		.domain(genres)
		.range([
			'#60a5fa',
			'#f59e0b',
			'#f87171',
			'#34d399',
			'#a78bfa',
			'#facc15',
			'#fb7185',
			'#38bdf8',
			'#94a3b8',
			'#c084fc'
		]);

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

<div class="chart-card">
	<h3>Scatterplot</h3>
	<p>Zusammenhang zwischen Erscheinungsjahr und globalen Verkaufszahlen.</p>

	<svg {width} {height} viewBox={`0 0 ${width} ${height}`}>
		<g transform={`translate(${margin.left}, ${margin.top})`}>

			{#each yTicks as tick}
				<line x1="0" x2={innerWidth} y1={yScale(tick)} y2={yScale(tick)} stroke="#e5edf6"/>
			{/each}

			{#each xTicks as tick}
				<line y1="0" y2={innerHeight} x1={xScale(tick)} x2={xScale(tick)} stroke="#f1f5f9"/>
			{/each}

			<line x1="0" x2={innerWidth} y1={innerHeight} y2={innerHeight} stroke="#94a3b8"/>
			<line x1="0" x2="0" y1="0" y2={innerHeight} stroke="#94a3b8"/>

			{#each xTicks as tick}
				<g transform={`translate(${xScale(tick)}, ${innerHeight})`}>
					<line y2="6" stroke="#94a3b8"/>
					<text y="22" text-anchor="middle" font-size="12" fill="#64748b">{tick}</text>
				</g>
			{/each}

			{#each yTicks as tick}
				<g transform={`translate(0, ${yScale(tick)})`}>
					<line x2="-6" stroke="#94a3b8"/>
					<text x="-10" y="4" text-anchor="end" font-size="12" fill="#64748b">{tick}</text>
				</g>
			{/each}

			<text x={innerWidth / 2} y={innerHeight + 48} text-anchor="middle" font-size="13" fill="#334155">
				Erscheinungsjahr
			</text>

			<text transform={`translate(-50, ${innerHeight / 2}) rotate(-90)`} text-anchor="middle" font-size="13" fill="#334155">
				Global Sales (Mio.)
			</text>

			{#each data as d}
				<circle
					cx={xScale(d.year)}
					cy={yScale(d.globalSales)}
					r={
						selected?.title === d.title
							? 14
							: hovered?.title === d.title
							? 11
							: 9
					}
					fill={color(d.genre)}
					opacity={selected?.title === d.title ? 1 : 0.85}
					stroke="#ffffff"
					stroke-width={selected?.title === d.title ? 3 : 1.5}
					style="transition: all 0.2s ease; cursor: pointer;"
					on:mouseenter={() => (hovered = d)}
					on:mouseleave={() => (hovered = null)}
					on:click={() => (selected = d)}
				/>
			{/each}

			<g transform={`translate(${innerWidth + 20}, 16)`}>
				<text x="0" y="-6" font-size="13" font-weight="700" fill="#334155">Legende</text>
				{#each genres as genre, i}
					<g transform={`translate(0, ${i * 24})`}>
						<rect width="14" height="14" rx="4" fill={color(genre)} />
						<text x="22" y="11" font-size="12" fill="#64748b">{genre}</text>
					</g>
				{/each}
			</g>

		</g>
	</svg>

	{#if selected}
		<div class="tooltip-box">
			<strong>{selected.title}</strong><br />
			Genre: {selected.genre}<br />
			Platform: {selected.platform}<br />
			Year: {selected.year}<br />
			Global Sales: {selected.globalSales} Mio.
		</div>
	{/if}
</div>