<svelte:options runes={false} />

<script>
	import * as d3 from 'd3';

	export let data = [];

	const width = 760;
	const height = 420;
	const margin = { top: 30, right: 30, bottom: 70, left: 70 };

	const innerWidth = width - margin.left - margin.right;
	const innerHeight = height - margin.top - margin.bottom;

	let hovered = null;

	$: salesByPlatform = Array.from(
		d3.rollup(
			data,
			(values) => d3.sum(values, (d) => d.globalSales),
			(d) => d.platform
		),
		([platform, sales]) => ({ platform, sales })
	).sort((a, b) => b.sales - a.sales);

	$: xScale = d3
		.scaleBand()
		.domain(salesByPlatform.map((d) => d.platform))
		.range([0, innerWidth])
		.padding(0.32);

	$: yScale = d3
		.scaleLinear()
		.domain([0, d3.max(salesByPlatform, (d) => d.sales) * 1.15])
		.range([innerHeight, 0]);

	$: yTicks = yScale.ticks(5);
</script>

<div class="chart-card">
	<h3>Column Chart</h3>
	<p>Vergleich der globalen Verkäufe pro Plattform.</p>

	<svg {width} {height} viewBox={`0 0 ${width} ${height}`}>
		<g transform={`translate(${margin.left}, ${margin.top})`}>
			{#each yTicks as tick}
				<line
					x1="0"
					x2={innerWidth}
					y1={yScale(tick)}
					y2={yScale(tick)}
					stroke="#e5edf6"
				/>
			{/each}

			<line x1="0" x2="0" y1="0" y2={innerHeight} stroke="#94a3b8" />
			<line x1="0" x2={innerWidth} y1={innerHeight} y2={innerHeight} stroke="#94a3b8" />

			{#each yTicks as tick}
				<g transform={`translate(0, ${yScale(tick)})`}>
					<line x2="-6" stroke="#94a3b8" />
					<text x="-10" y="4" text-anchor="end" font-size="12" fill="#64748b">
						{tick}
					</text>
				</g>
			{/each}

			{#each salesByPlatform as d}
				<rect
					x={xScale(d.platform)}
					y={yScale(d.sales)}
					width={xScale.bandwidth()}
					height={innerHeight - yScale(d.sales)}
					rx="12"
					fill={hovered?.platform === d.platform ? '#2563eb' : '#60a5fa'}
					role="presentation"
					aria-hidden="true"
					style="transition: all 0.2s ease;"
					on:mouseenter={() => (hovered = d)}
					on:mouseleave={() => (hovered = null)}
				/>

				<text
					x={xScale(d.platform) + xScale.bandwidth() / 2}
					y={yScale(d.sales) - 10}
					text-anchor="middle"
					font-size="12"
					fill="#475569"
				>
					{d.sales.toFixed(0)}
				</text>
			{/each}

			{#each salesByPlatform as d}
				<text
					x={xScale(d.platform) + xScale.bandwidth() / 2}
					y={innerHeight + 24}
					text-anchor="middle"
					font-size="12"
					fill="#64748b"
				>
					{d.platform}
				</text>
			{/each}

			<text
				x={innerWidth / 2}
				y={innerHeight + 52}
				text-anchor="middle"
				font-size="13"
				fill="#334155"
			>
				Plattform
			</text>

			<text
				transform={`translate(-50, ${innerHeight / 2}) rotate(-90)`}
				text-anchor="middle"
				font-size="13"
				fill="#334155"
			>
				Global Sales (Mio.)
			</text>
		</g>
	</svg>

	{#if hovered}
		<div class="tooltip-box">
			<strong>{hovered.platform}</strong><br />
			Global Sales: {hovered.sales.toFixed(1)} Mio.
		</div>
	{/if}
</div>