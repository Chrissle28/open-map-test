<template>
	<div class="map-wrap">
		<a href="https://www.maptiler.com" class="watermark"
			><img
				src="https://api.maptiler.com/resources/logo.svg"
				alt="MapTiler logo"
		/></a>
		<div class="map" ref="mapContainer"></div>
	</div>
</template>

<script setup>
import { Map, NavigationControl, Marker } from 'maplibre-gl';
import { shallowRef, onMounted, onUnmounted, markRaw } from 'vue';

const mapContainer = shallowRef(null);
const map = shallowRef(null);

const geojson = {
	type: 'FeatureCollection',
	features: [
		{
			type: 'Feature',
			properties: {
				message: 'Foo',
				iconSize: [60, 60],
			},
			geometry: {
				type: 'Point',
				coordinates: [10.1653, 54.321],
			},
		},
		{
			type: 'Feature',
			properties: {
				message: 'Bar',
				iconSize: [50, 50],
			},
			geometry: {
				type: 'Point',
				coordinates: [10.1553, 54.321],
			},
		},
		{
			type: 'Feature',
			properties: {
				message: 'Baz',
				iconSize: [40, 40],
			},
			geometry: {
				type: 'Point',
				coordinates: [10.1453, 54.321],
			},
		},
	],
};

onMounted(() => {
	const apiKey = 'y3u9ZESfXC3pE1p4fmPi';

	const initialState = { lng: 10.1353, lat: 54.321, zoom: 8 };

	map.value = markRaw(
		new Map({
			container: mapContainer.value,
			style: `https://api.maptiler.com/maps/streets/style.json?key=${apiKey}`,
			center: [initialState.lng, initialState.lat],
			zoom: initialState.zoom,
		})
	);
	map.value.addControl(new NavigationControl(), 'top-right');
	new Marker({ color: '#FF0000' })
		.setLngLat([10.1353, 54.321])
		.addTo(map.value);
	geojson.features.forEach((marker) => {
		new Marker({ color: '#0000cc' })
			// .setPopup(new mapboxgl.Popup().setHTML('<h1>Hello World!</h1>'))
			.setLngLat(marker.geometry.coordinates)
			.addTo(map.value);
	});
	setTimeout(() => {
		map.value.zoomTo(14, {
			duration: 2000,
		});
		setTimeout(() => {
			map.value.zoomTo(8, {
				duration: 2000,
			});
		}, 5000);
	}, 6000);
}),
	onUnmounted(() => {
		map.value?.remove();
	});
</script>

<style scoped>
.marker {
	display: block;
	border: none;
	border-radius: 50%;
	cursor: pointer;
	padding: 0;
}
.map-wrap {
	position: relative;
	width: 100%;
	height: calc(100vh); /* calculate height of the screen minus the heading */
}

.map {
	position: absolute;
	width: 100%;
	height: 100%;
}

.watermark {
	position: absolute;
	left: 10px;
	bottom: 10px;
	z-index: 999;
}
</style>
