<svelte:options runes={false} />

<script>
	import PieChart from '$lib/components/PieChart.svelte';
	import ColumnChart from '$lib/components/ColumnChart.svelte';
	import ScatterPlot from '$lib/components/ScatterPlot.svelte';
	import { games } from '$lib/data/games.js';
</script>

<svelte:head>
	<title>svgCharts | Video Game Data Visualization</title>
	<meta
		name="description"
		content="Interaktive Datenvisualisierung von Videospielen mit Pie Chart, Column Chart und Scatterplot."
	/>
</svelte:head>

<div class="page">
	<section class="hero">
		<div class="hero-content">
			<div class="badge">D3.js • SVG • Svelte • Visual Data Project</div>

			<h1><span class="gradient-text">svgCharts</span></h1>

			<p>
				Eine moderne, interaktive Website zur Visualisierung von Videospiel-Daten.
				Die Seite zeigt Verkaufszahlen, Plattformen, Genres und zeitliche Zusammenhänge
				in drei unterschiedlichen SVG-Charts.
			</p>

			<div class="hero-stats">
				<div class="stat-pill">
					<strong>18</strong>
					<span>Videospiele im Datensatz</span>
				</div>

				<div class="stat-pill">
					<strong>3</strong>
					<span>Interaktive Diagrammtypen</span>
				</div>

				<div class="stat-pill">
					<strong>D3 + SVG</strong>
					<span>Technische Basis der Visualisierung</span>
				</div>
			</div>
		</div>
	</section>

	<section class="section container">
		<div class="section-heading">
			<h2>Über das Projekt</h2>
			<p>
				Dieses Projekt verbindet D3.js, SVG und Svelte zu einer interaktiven
				Datenvisualisierung. Die Daten sind fix im Projekt eingebaut und werden
				ohne User-Eingabe in drei verschiedene Diagramme übertragen. Dadurch können
				Zusammenhänge schneller erkannt und visuell ansprechend dargestellt werden.
			</p>
		</div>
	</section>

	<section class="section container">
		<div class="section-heading">
			<h2>Was zeigen die Charts?</h2>
			<p>
				Jeder Chart betrachtet dieselben Videospiel-Daten aus einer anderen Perspektive:
				Genre-Verteilung, Plattform-Performance und Zusammenhang zwischen Jahr und Verkäufen.
			</p>
		</div>

		<div class="card-grid">
			<div class="mini-card">
				<h3>Pie Chart</h3>
				<p>
					Zeigt die Verteilung der globalen Verkaufszahlen nach Genre und macht schnell sichtbar,
					welche Spielarten am stärksten vertreten sind.
				</p>
			</div>

			<div class="mini-card">
				<h3>Column Chart</h3>
				<p>
					Vergleicht Plattformen miteinander und zeigt, auf welchen Systemen die höchsten
					globalen Verkaufszahlen erzielt wurden.
				</p>
			</div>

			<div class="mini-card">
				<h3>Scatterplot</h3>
				<p>
					Stellt den Zusammenhang zwischen Erscheinungsjahr und globalen Verkäufen dar.
					Jeder Punkt steht für ein einzelnes Spiel.
				</p>
			</div>
		</div>
	</section>

	<section class="charts-section container">
		<div class="single-chart">
			<PieChart data={games} />
		</div>

		<div class="single-chart">
			<ColumnChart data={games} />
		</div>

		<div class="single-chart">
			<ScatterPlot data={games} />
		</div>
	</section>

	<div class="footer-note container">
		<p>Interaktive SVG-Charts mit D3.js und Svelte</p>
	</div>
</div>