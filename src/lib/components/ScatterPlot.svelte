<svelte:options runes={false} />

<script>
	import * as d3 from 'd3';

	export let data = [];

	const width = 760;
	const height = 420;
	const margin = { top: 30, right: 30, bottom: 70, left: 70 };

	const innerWidth = width - margin.left - margin.right;
	const innerHeight = height - margin.top - margin.bottom;

	const colors = {
		Sandbox: "#3b82f6",
		Action: "#f59e0b",
		Sports: "#ef4444",
		"Battle Royale": "#10b981",
		Racing: "#8b5cf6",
		RPG: "#eab308",
		Shooter: "#fb7185",
		Simulation: "#38bdf8",
		MOBA: "#64748b",
		Adventure: "#a78bfa"
	};

	let hovered = null;
	let mouseX = 0;
	let mouseY = 0;

	function handleMove(e) {
		mouseX = e.clientX;
		mouseY = e.clientY;
	}

	$: xScale = d3.scaleLinear()
		.domain(d3.extent(data, d => d.year))
		.range([0, innerWidth]);

	$: yScale = d3.scaleLinear()
		.domain([0, d3.max(data, d => d.globalSales) * 1.15])
		.range([innerHeight, 0]);

	$: xTicks = xScale.ticks(5);
	$: yTicks = yScale.ticks(5);
</script>

<div class="chart-card" on:mousemove={handleMove}>
	<h3>Scatterplot</h3>
	<p>Erscheinungsjahr vs Verkäufe.</p>

	<!-- 🔥 GLEICHES LAYOUT WIE COLUMN -->
	<div class="chart-layout">

		<svg {width} {height}>
			<g transform={`translate(${margin.left}, ${margin.top})`}>

				<!-- GRID -->
				{#each yTicks as tick}
					<line x1="0" x2={innerWidth} y1={yScale(tick)} y2={yScale(tick)} stroke="#e5edf6"/>
				{/each}

				<!-- AXES -->
				<line x1="0" x2="0" y1="0" y2={innerHeight} stroke="#94a3b8"/>
				<line x1="0" x2={innerWidth} y1={innerHeight} y2={innerHeight} stroke="#94a3b8"/>

				<!-- Y TICKS -->
				{#each yTicks as tick}
					<g transform={`translate(0, ${yScale(tick)})`}>
						<line x2="-6" stroke="#94a3b8"/>
						<text x="-10" y="4" text-anchor="end" font-size="12" fill="#64748b">{tick}</text>
					</g>
				{/each}

				<!-- X TICKS -->
				{#each xTicks as tick}
					<g transform={`translate(${xScale(tick)}, ${innerHeight})`}>
						<line y2="6" stroke="#94a3b8"/>
						<text y="20" text-anchor="middle" font-size="12" fill="#64748b">{tick}</text>
					</g>
				{/each}

				<!-- POINTS -->
				{#each data as d}
					<circle
						cx={xScale(d.year)}
						cy={yScale(d.globalSales)}
						r="6"
						fill={colors[d.genre]}
						on:mouseenter={() => (hovered = d)}
						on:mouseleave={() => (hovered = null)}
					/>
				{/each}

				<!-- LABELS -->
				<text x={innerWidth/2} y={innerHeight + 50} text-anchor="middle">
					Jahr
				</text>

				<text transform={`translate(-50, ${innerHeight/2}) rotate(-90)`} text-anchor="middle">
					Global Sales (Mio.)
				</text>

			</g>
		</svg>

		<!-- 👉 LEGEND RECHTS (GLEICH WIE COLUMN) -->
		<div class="legend">
			<h4>Genres</h4>

			{#each Object.entries(colors) as [genre, color]}
				<div class="legend-item">
					<span class="legend-color" style={`background:${color}`}></span>
					<span>{genre}</span>
				</div>
			{/each}
		</div>

	</div>

	{#if hovered}
		<div class="tooltip-box" style={`left:${mouseX+16}px; top:${mouseY+16}px;`}>
			<strong>{hovered.title}</strong><br />
			{hovered.genre} <br />
			{hovered.globalSales} Mio.
		</div>
	{/if}
</div>