<script>
	import * as d3 from 'd3';

	export let data = [];

	let svg;
	let tooltip;

	$: if (svg && data.length > 0) {
		drawChart();
	}

	function drawChart() {
		const width = 420;
		const height = 320;
		const radius = Math.min(width, height) / 2 - 20;

		const genreTotals = d3
			.rollups(
				data,
				(v) => d3.sum(v, (d) => d.Global_Sales),
				(d) => d.Genre
			)
			.map(([Genre, Sales]) => ({ Genre, Sales }))
			.sort((a, b) => d3.descending(a.Sales, b.Sales));

		const color = d3
			.scaleOrdinal()
			.domain(genreTotals.map((d) => d.Genre))
			.range(d3.schemeSet2);

		const pie = d3.pie().value((d) => d.Sales).sort(null);

		const arc = d3.arc().innerRadius(0).outerRadius(radius);

		const svgEl = d3.select(svg);
		svgEl.selectAll('*').remove();

		const root = svgEl.attr('width', width).attr('height', height);

		const g = root.append('g').attr('transform', `translate(${width / 2}, ${height / 2 + 10})`);

		g.selectAll('path')
			.data(pie(genreTotals))
			.enter()
			.append('path')
			.attr('d', arc)
			.attr('fill', (d) => color(d.data.Genre))
			.attr('stroke', '#0f172a')
			.attr('stroke-width', 2)
			.on('mousemove', (event, d) => {
				tooltip.style.opacity = '1';
				tooltip.style.left = `${event.pageX + 12}px`;
				tooltip.style.top = `${event.pageY - 28}px`;
				tooltip.innerHTML = `
					<strong>${d.data.Genre}</strong><br>
					Sales: ${d.data.Sales.toFixed(1)} Mio.
				`;
			})
			.on('mouseleave', () => {
				tooltip.style.opacity = '0';
			});

		const legend = root.append('g').attr('transform', 'translate(12, 12)');

		genreTotals.slice(0, 6).forEach((d, i) => {
			const row = legend.append('g').attr('transform', `translate(0, ${i * 22})`);

			row.append('rect').attr('width', 14).attr('height', 14).attr('rx', 3).attr('fill', color(d.Genre));

			row.append('text')
				.attr('x', 20)
				.attr('y', 11)
				.attr('fill', '#e5e7eb')
				.attr('font-size', '12px')
				.text(d.Genre);
		});
	}
</script>

<div class="chart-card">
	<h2>Pie Chart – Sales nach Genre</h2>
	<svg bind:this={svg}></svg>
	<div class="tooltip" bind:this={tooltip}></div>
</div>

<style>
	.chart-card {
		background: #111827;
		padding: 1rem;
		border-radius: 18px;
		box-shadow: 0 10px 25px rgba(0, 0, 0, 0.25);
		position: relative;
		min-height: 430px;
	}

	h2 {
		margin: 0 0 1rem 0;
		color: white;
		font-size: 1.1rem;
	}

	svg {
		display: block;
		margin: 0 auto;
		max-width: 100%;
		height: auto;
	}

	.tooltip {
		position: fixed;
		background: rgba(0, 0, 0, 0.9);
		color: white;
		padding: 0.55rem 0.75rem;
		border-radius: 8px;
		font-size: 0.9rem;
		pointer-events: none;
		opacity: 0;
		transition: opacity 0.15s ease;
		z-index: 1000;
	}
</style>