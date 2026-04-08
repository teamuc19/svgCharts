<svelte:options runes={false} />

<script>
	import * as d3 from 'd3';

	export let data = [];

	let hovered = null;
	let mouseX = 0;
	let mouseY = 0;

	function handleMove(e) {
		mouseX = e.clientX;
		mouseY = e.clientY;
	}

	const width = 420;
	const height = 420;
	const radius = 132;

	$: salesByGenre = Array.from(
		d3.rollup(data, v => d3.sum(v, d => d.globalSales), d => d.genre),
		([genre, sales]) => ({ genre, sales })
	);

	$: color = d3.scaleOrdinal()
		.domain(salesByGenre.map(d => d.genre))
		.range([
			'#60a5fa','#f59e0b','#f87171','#34d399',
			'#a78bfa','#facc15','#fb7185','#38bdf8',
			'#94a3b8','#c084fc'
		]);

	$: pieData = d3.pie().value(d => d.sales).sort(null)(salesByGenre);
	$: arc = d3.arc().innerRadius(0).outerRadius(radius);
</script>

<div class="chart-card" on:mousemove={handleMove}>
	<h3>Pie Chart</h3>
	<p>Verteilung der globalen Verkäufe nach Genre.</p>

	<div class="chart-layout">
		<svg {width} {height}>
			<g transform={`translate(${width/2}, ${height/2})`}>
				{#each pieData as p}
					<path
						d={arc(p)}
						fill={color(p.data.genre)}
						stroke="#ffffff"
						stroke-width="3"
						stroke-linejoin="round"
						opacity={hovered?.genre === p.data.genre ? 1 : 0.95}
						transform={hovered?.genre === p.data.genre ? 'scale(1.05)' : 'scale(1)'}
						style="transition: all 0.2s ease;"
						on:mouseenter={() => (hovered = p.data)}
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
		<div class="tooltip-box" style={`left:${mouseX + 16}px; top:${mouseY + 16}px;`}>
			<strong>{hovered.genre}</strong><br />
			Global Sales: {hovered.sales.toFixed(1)} Mio.
		</div>
	{/if}
</div>