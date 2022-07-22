<script>
	import { onMount } from 'svelte';
	import { fabric } from "fabric-pure-browser";

	const products = [
		{
            label: "Strike 2",
			price: 1360,
			weight: 2400,
            category: 2,
			img: './images/strike2.png',
		},
		{
            label: "Stayup",
			price: 1422,
			weight: 1790,
            category: 1,
			img: './images/stayup.png',
		},
		{
            label: "Kolibri",
			price: 1300,
			weight: 1900,
            category: 3,
			img: './images/kolibri.png',
		},
		{
            label: "GTO light 2",
			price: 1400,
			weight: 3345,
            category: 3,
			img: './images/gto-light-2.png',
		},
		{
            label: "Genie Lite 3",
			price: 1625,
			weight: 4800,
            category: 3,
			img: './images/genie-lite-3.png',
		},
		{
            label: "Genie X-lite",
			price: 1990,
			weight: 3210,
            category: 3,
			img: './images/genie-x-lite.png'
		},
		{
            label: "Suspender",
			price: 1700,
			weight: 3850,
            category: 3,
			img: './images/suspender.png',
		},
		{
            label: "Delight 3",
			price: 1300,
			weight: 3700,
            category: 3,
			img: './images/delight-3.png',
		},
		{
			label: "Kanibal Race 2",
			price: 2200,
			weight: 7500,
			img: './images/kanibal-race-2.png',
		},
		{
			label: "BV1",
			price: 1350,
			weight: 2000,
			img: './images/bv1.png',
		},
		{
			label: "F* Race",
			price: 1430,
			weight: 1500,
			img: './images/f-race.png',
		},
		{
			label: "Impress 4",
			price: 2310,
			weight: 6600,
			img: './images/impress-4.png',
		},
		{
			label: "Weightless",
			price: 1700,
			weight: 2000,
			img: './images/weightless.png',
		},
		{
			label: "Lightness 3",
			price: 1460,
			weight: 3500,
			img: './images/lightness-3.png',
		},
	];

	/**
	 * @type {HTMLCanvasElement}
	 */
	let canvas;
	const maxPrice = products.reduce((prev, curr) => curr.price > prev ? curr.price : prev, 0);
	const maxWeight = products.reduce((prev, curr) => curr.weight > prev ? curr.weight : prev, 0);
	const minPrice = products.reduce((prev, curr) => curr.price < prev ? curr.price : prev, Infinity);
	const minWeight = products.reduce((prev, curr) => curr.weight < prev ? curr.weight : prev, Infinity);
	let priceFilter = maxPrice
	let weightFilter = maxWeight
    const WIDTH = 1800
    const HEIGHT = 800

	/**
	 * @type {fabric.Canvas}
	 */
	let canv;

	const draw = () => {
		// console.log('draw');
		// ctx.clearRect(0, 0, WIDTH, HEIGHT);
        // for (const product of products.filter(p => p.price <= maxPrice && p.weight <= weightFilter)) {
        //     const x = product.weight/4 - 300
        //     const y = HEIGHT - product.price/2 + 400
        //     // circle(x, y, 'red', product.category*30);
        //     ctx.font = '16px sans-serif';
        //     ctx.textAlign = "center"
        //     // ctx.fillText(product.label, x, y+4);

		// 	ctx.drawImage(product.image, x, y, 200, 200 * product.image.height / product.image.width)
        // }

		for (const product of products) {
			if (product.price <= priceFilter && product.weight <= weightFilter) {
				product.object.visible = true
			} else {
				product.object.visible = false
			}
		}
		canv.requestRenderAll()
	};

	onMount(() => {
        canv = new fabric.Canvas(canvas, {width: WIDTH, height: HEIGHT, interactive: false, selection: false});

		const text = new fabric.Text('Click on the images for details', { left: 10, top: 10, fontFamily: 'sans-serif'});
		canv.add(text)

		for (const product of products) {
			const y = HEIGHT - product.weight/9 +100
			const x = (product.price- 1280)*1.6 



			fabric.Image.fromURL(product.img, img => {img.on('selected', () => text.text = [
				product.label,
				`${product.price}€`,
				`${product.weight/1000}kg`
			].join('\n'));canv.add(img);product.object = img}, {
				scaleX: 0.25,
				scaleY: 0.25,
				left: x,
				top: y,
				lockMovementX: true,
				lockMovementY: true,
				lockScalingX: true,
				lockScalingY: true,
				hoverCursor: 'pointer'
			});
		}
	});

</script>

<canvas bind:this={canvas} />

<div>
	<label for="priceFilter">Prix max :</label>
	<input name="priceFilter" type="range" min={minPrice} max={maxPrice} bind:value={priceFilter} on:input={draw} />{priceFilter}€
</div>
<div>
	<label for="weightFilter">Poid max :</label>
	<input name="weightFilter" type="range" min={minWeight} max={maxWeight} bind:value={weightFilter} on:input={draw} />{weightFilter}g
</div>

<style>
	canvas {
        border: 1px solid black
	}
</style>
