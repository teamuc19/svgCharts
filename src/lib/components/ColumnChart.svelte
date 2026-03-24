<script>
	import * as d3 from 'd3';

	export let data = [];

	let svg;
	let tooltip;

	$: if (svg && data.length > 0) {
		drawChart();
	}

	function drawChart() {
		const margin = { top: 20, right: 20, bottom: 70, left: 65 };
		const width = 520 - margin.left - margin.right;
		const height = 350 - margin.top - margin.bottom;

		const platformTotals = d3
			.rollups(
				data,
				(v) => d3.sum(v, (d) => d.Global_Sales),
				(d) => d.Platform
			)
			.map(([Platform, Sales]) => ({ Platform, Sales }))
			.sort((a, b) => d3.descending(a.Sales, b.Sales))
			.slice(0, 8);

		const svgEl = d3.select(svg);
		svgEl.selectAll('*').remove();

		const root = svgEl
			.attr('width', width + margin.left + margin.right)
			.attr('height', height + margin.top + margin.bottom);

		const g = root.append('g').attr('transform', `translate(${margin.left},${margin.top})`);

		const x = d3
			.scaleBand()
			.domain(platformTotals.map((d) => d.Platform))
			.range([0, width])
			.padding(0.28);

		const y = d3
			.scaleLinear()
			.domain([0, d3.max(platformTotals, (d) => d.Sales)])
			.nice()
			.range([height, 0]);

		const xAxis = g
			.append('g')
			.attr('transform', `translate(0,${height})`)
			.call(d3.axisBottom(x));

		xAxis.selectAll('text').attr('fill', '#e5e7eb').attr('font-size', '12px');
		xAxis.selectAll('.domain, .tick line').attr('stroke', '#6b7280');

		const yAxis = g.append('g').call(d3.axisLeft(y));

		yAxis.selectAll('text').attr('fill', '#e5e7eb').attr('font-size', '12px');
		yAxis.selectAll('.domain, .tick line').attr('stroke', '#6b7280');

		g.selectAll('rect.bar')
			.data(platformTotals)
			.enter()
			.append('rect')
			.attr('class', 'bar')
			.attr('x', (d) => x(d.Platform))
			.attr('y', (d) => y(d.Sales))
			.attr('width', x.bandwidth())
			.attr('height', (d) => height - y(d.Sales))
			.attr('rx', 8)
			.attr('fill', '#60a5fa')
			.on('mousemove', (event, d) => {
				tooltip.style.opacity = '1';
				tooltip.style.left = `${event.pageX + 12}px`;
				tooltip.style.top = `${event.pageY - 28}px`;
				tooltip.innerHTML = `
					<strong>${d.Platform}</strong><br>
					Sales: ${d.Sales.toFixed(1)} Mio.
				`;
			})
			.on('mouseleave', () => {
				tooltip.style.opacity = '0';
			});

		g.append('text')
			.attr('x', width / 2)
			.attr('y', height + 50)
			.attr('text-anchor', 'middle')
			.attr('fill', '#e5e7eb')
			.text('Plattform');

		g.append('text')
			.attr('transform', 'rotate(-90)')
			.attr('x', -height / 2)
			.attr('y', -45)
			.attr('text-anchor', 'middle')
			.attr('fill', '#e5e7eb')
			.text('Globale Sales (Mio.)');
	}
</script>

<div class="chart-card">
	<h2>Column Chart – Verkäufe nach Plattform</h2>
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