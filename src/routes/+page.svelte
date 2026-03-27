<svelte:options runes={false} />

<script>
	import PieChart from '$lib/components/PieChart.svelte';
	import ColumnChart from '$lib/components/ColumnChart.svelte';
	import ScatterPlot from '$lib/components/ScatterPlot.svelte';
	import { games } from '$lib/data/games.js';
</script>

<svelte:head>
	<title>SVG CHARTS</title>
	<meta
		name="description"
		content="Interaktive Visualisierung von Videospiel-Daten mit Pie Chart, Column Chart und Scatterplot."
	/>
</svelte:head>

<div class="page">
	<section class="hero">
		<div class="container hero-simple">
			<h1>SVG CHARTS</h1>
			<p>
				Diese Website zeigt interaktive Diagramme mit SVG. Im Rahmen des Kurses werden
				Daten mit unterschiedlichen Chart-Typen visuell dargestellt und strukturiert
				analysiert.
			</p>
		</div>
	</section>

	<section class="section container">
		<div class="section-header">
			<div class="section-kicker">Allgemeine Informationen</div>
			<h2>Über SVG Charts</h2>
			<p>
				SVG-Charts ermöglichen eine klare und skalierbare Darstellung von Daten im Browser.
				Sie eignen sich besonders für interaktive Visualisierungen, weil Formen, Farben,
				Beschriftungen und Hover-Effekte präzise gesteuert werden können.
			</p>
			<p>
				In diesem Projekt werden dieselben Daten in drei verschiedenen Diagrammtypen
				dargestellt: Pie Chart, Column Chart und Scatterplot. Dadurch lassen sich Daten
				aus mehreren Perspektiven vergleichen und besser interpretieren.
			</p>
		</div>
	</section>

	<section class="charts-section container">
		<div class="section-header">
			<div class="section-kicker">Visualisierungen</div>
			<h2>Interaktive Diagramme</h2>
			<p>
				Die folgenden Diagramme basieren auf demselben Datensatz und zeigen die Informationen
				in unterschiedlicher Form.
			</p>
		</div>

		<div class="chart-block">
			<PieChart data={games} />
		</div>

		<div class="chart-block">
			<ColumnChart data={games} />
		</div>

		<div class="chart-block">
			<ScatterPlot data={games} />
		</div>
	</section>

	<div class="footer container">
		<p>Projekt zur Datenvisualisierung mit SVG-Charts</p>
	</div>
</div>