<script>
	import { onMount } from 'svelte';
	import { fabric } from "fabric";

	import img_strike2 from '$lib/assets/strike2.png';
	import img_stayup from '$lib/assets/stayup.png';
	import img_kolibri from '$lib/assets/kolibri.png';
	import img_gto_light_2 from '$lib/assets/gto-light-2.png';
	import img_genie_lite_3 from '$lib/assets/genie-lite-3.png';
	import img_genie_x_lite from '$lib/assets/genie-x-lite.png';
	import img_suspender from '$lib/assets/suspender.png';
	import img_delight_3 from '$lib/assets/delight-3.png';
	import img_delight_4 from '$lib/assets/delight-4.png';
	import img_delight_4_sport from '$lib/assets/delight-4-sport.png';
	import img_kanibal_race_2 from '$lib/assets/kanibal-race-2.png';
	import img_bv1 from '$lib/assets/bv1.png';
	import img_f_race from '$lib/assets/f-race.png';
	import img_impress_4 from '$lib/assets/impress-4.png';
	import img_weightless from '$lib/assets/weightless.png';
	import img_lightness_3 from '$lib/assets/lightness-3.png';
	import img_grass_hopper from '$lib/assets/grass-hopper.png';

	const products = [
		{
            label: "Strike 2",
			price: 1360,
			weight: 2400,
            category: 2,
			img: img_strike2,
			brand: "Supair",
			rescueLink: "shoulders",
			rescuePlacement: "ventral",
		},
		{
            label: "Stayup",
			price: 1422,
			weight: 1790,
            category: 1,
			img: img_stayup,
			brand: "Neo",
			rescueLink: "carabiners",
			rescuePlacement: "ventral",
		},
		{
            label: "Kolibri",
			price: 1300,
			weight: 1900,
            category: 3,
			img: img_kolibri,
			brand: "Kortel",
			rescueLink: "carabiners",
			rescuePlacement: "ventral",
		},
		{
            label: "GTO light 2",
			price: 1400,
			weight: 3345,
            category: 3,
			img: img_gto_light_2,
			brand: "Woody Valley",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
            label: "Genie Lite 3",
			price: 1625,
			weight: 4800,
            category: 3,
			img: img_genie_lite_3,
			brand: "Gin",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
            label: "Genie X-lite",
			price: 1990,
			weight: 3210,
            category: 3,
			img: img_genie_x_lite,
			brand: "Gin",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
            label: "Suspender",
			price: 1700,
			weight: 3850,
            category: 3,
			img: img_suspender,
			brand: "Neo",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
            label: "Delight 3",
			price: 1300,
			weight: 3700,
            category: 3,
			img: img_delight_3,
			brand: "Supair",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
            label: "Delight 4",
			price: 1590,
			weight: 3770,
            category: 3,
			img: img_delight_4,
			brand: "Supair",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
            label: "Delight 4 Sport",
			price: 1750,
			weight: 3770,
            category: 3,
			img: img_delight_4_sport,
			brand: "Supair",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
			label: "Kanibal Race 2",
			price: 2200,
			weight: 7500,
			img: img_kanibal_race_2,
			brand: "Kortel",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
			label: "BV1",
			price: 1350,
			weight: 2000,
			img: img_bv1,
			brand: "Ozone",
			rescueLink: "shoulders",
			rescuePlacement: "ventral",
		},
		{
			label: "F* Race",
			price: 1430,
			weight: 1500,
			img: img_f_race,
			brand: "Ozone",
			rescueLink: "shoulders",
			rescuePlacement: "ventral",
		},
		{
			label: "Impress 4",
			price: 2310,
			weight: 6600,
			img: img_impress_4,
			brand: "Advance",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
			label: "Weightless",
			price: 1700,
			weight: 2000,
			img: img_weightless,
			brand: "Advance",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
			label: "Lightness 3",
			price: 1460,
			weight: 3500,
			img: img_lightness_3,
			brand: "Advance",
			rescueLink: "shoulders",
			rescuePlacement: "underseat",
		},
		{
			label: "Grass Hopper",
			price: 1590,
			weight: 2600,
			img: img_grass_hopper,
			brand: "Little Cloud",
			rescueLink: "carabiners",
			rescuePlacement: "ventral",
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
	let mustHaveUnderseat = false
	let mustHaveShoulderLinkRescue = false
    let WIDTH = 1400
    let HEIGHT = 800
	let lines = []
	let lineTexts = []
	/**
	 * @type {URLSearchParams}
	 */
	let urlParams

	/**
	 * @type {fabric.Canvas}
	 */
	let canv;

	const matchesFilters = (product) => {
		if (mustHaveUnderseat && product.rescuePlacement !== 'underseat') {
			return false
		}
		if (mustHaveShoulderLinkRescue && product.rescueLink !== 'shoulders') {
			return false
		}
		if (product.price > priceFilter) {
			return false 
		}
		if (product.weight > weightFilter) {
			return false 
		}
		return true
	}

	const draw = (priceFilter, weightFilter, mustHaveUnderseat, mustHaveShoulderLinkRescue) => {
		if (!canv || !products[0].object) {
			return
		}
		urlParams.set('maxPrice', `${priceFilter}`)
		urlParams.set('maxWeight', `${weightFilter}`)
		window.history.replaceState(null, null, `?${urlParams.toString()}`)
		drawGrid()

		for (const product of products) {
			if (matchesFilters(product)) {
				product.object.visible = true
			} else {
				product.object.visible = false
			}

			const x = priceToX(product.price) 
			const y = weightToY(product.weight)
			product.object.set({left: x, top: y})
			product.object.setCoords()
			product.object.bringToFront()
		}

		canv.getObjects().forEach((object) => {
			object.selectable = false
			object.hoverCursor = 'auto'
		})
		canv.requestRenderAll()
	};

	$: draw(priceFilter, weightFilter, mustHaveUnderseat, mustHaveShoulderLinkRescue)

	const priceToX = price => {
		const priceRange = priceFilter - minPrice
		return (price-minPrice+5) / (priceRange+100) * WIDTH
	}

	const weightToY = weight => {
		const weightRange = weightFilter - minWeight
		return HEIGHT - ((weight-minWeight+500) / (weightRange+600) * HEIGHT)
	}

	const getArrow = (label) => {
		const lineLength = 130
		const color = '#777'
		const lineBefore = new fabric.Line([0, HEIGHT-20, lineLength, HEIGHT-20], {stroke: color})
		const text = new fabric.Text(label, {
			left: lineLength + 10,
			top: HEIGHT-30,
			fontFamily: 'sans-serif',
			fontSize: 18,
			fill: color,
			fontWeight: 'lighter',
		});
		const textLength = text.get('width')
		const end = lineLength + 10 + textLength + 10 + lineLength
		const lineAfter = new fabric.Line([lineLength + 10 + textLength + 10, HEIGHT-20, end, HEIGHT-20], {stroke: color})
		const triangle = new fabric.Triangle({
            width: 10, 
            height: 10, 
            fill: color, 
            left: end, 
            top: HEIGHT-25,
            angle: 90
        });
		return new fabric.Group([text, triangle, lineBefore, lineAfter])
	}

	const drawGrid = () => {
		canv.remove(...lines)
		canv.remove(...lineTexts)
		lines = []
		lineTexts = []
		for (let i = 2000; i < maxWeight; i+=1000) {
			const line = new fabric.Line([0, weightToY(i), WIDTH, weightToY(i)], {stroke: '#eee'})
			canv.add(line)
			lines.push(line)
			const text = new fabric.Text(`${i/1000}kg`, {
				left: WIDTH - 75,
				top: weightToY(i),
				fontFamily: 'sans-serif',
				fontSize: 12,
				fill: '#777',
				fontWeight: 'lighter',
			});
			canv.add(text)
			lineTexts.push(text)
		}
		for (let i = 1000; i < maxPrice; i+=200) {
			const line = new fabric.Line([priceToX(i), 0, priceToX(i), HEIGHT], {stroke: '#eee'})
			canv.add(line)
			lines.push(line)
			const text = new fabric.Text(`${i}€`, {
				left: priceToX(i),
				top: 0,
				fontFamily: 'sans-serif',
				fontSize: 12,
				fill: '#777',
				fontWeight: 'lighter',
			});
			canv.add(text)
			lineTexts.push(text)
		}
	}

	const selectProduct = (product) => {
		document.getElementById('productName').textContent = product.label
		document.getElementById('productPrice').textContent = `💰 ${product.price}€`
		document.getElementById('productWeight').textContent =  `⚖️ ${product.weight/1000}kg`
		document.getElementById('productBrand').textContent = `🏷 ${product.brand}`
	}

	const drawProducts = () => {
		for (const product of products) {
			const y = weightToY(product.weight)
			const x = priceToX(product.price) 

			fabric.Image.fromURL(product.img, img => {
				// img.on('selected', () => selectProduct(product))
				img.on('mouseover', () => selectProduct(product))
				canv.add(img)
				product.object = img
		    }, {
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
	}

	onMount(() => {
		urlParams = new URLSearchParams(window.location.search)
		if (urlParams.get('maxPrice')) {
			priceFilter = parseInt(urlParams.get('maxPrice'), 10)
		}
		if (urlParams.get('maxWeight')) {
			weightFilter = parseInt(urlParams.get('maxWeight'), 10)
		}


		WIDTH = window.innerWidth - 350
		HEIGHT = window.innerHeight - 30
        canv = new fabric.Canvas(canvas, {width: WIDTH, height: HEIGHT, interactive: false, selection: false});
		drawGrid()
		// grid = getGrid()
		// canv.add(grid)
		

		const priceArrow = getArrow('PRIX')
		priceArrow.set({left: 600})
		canv.add(priceArrow)
		const weightArrow = getArrow('POIDS')
		weightArrow.set({angle: -90, left: WIDTH - 30})
		canv.add(weightArrow)

		drawProducts()
		canv.getObjects().forEach((object) => {
			object.selectable = false
			object.hoverCursor = 'auto'
		})
	});

</script>

<div class="flex p-2">
	<div class="grow mr-2">
		<div class="border border-black p-2">
			<div id="productName" class="text-3xl pb-2">Passer la souris sur un produit</div>
			<div id="productPrice" class="text-xl"></div>
			<div id="productWeight" class="text-xl"></div>
			<div id="productBrand" class="text-xl"></div>
	    </div>

		<p class="text-xl pt-8 pb-2">Filtres</p>
		<div>
			<label for="priceFilter">Prix max:</label>
			<input name="priceFilter" type="range" min={minPrice} max={maxPrice} step="10" bind:value={priceFilter} />{priceFilter}€
		</div>
		<div>
			<label for="weightFilter">Poids max:</label>
			<input name="weightFilter" type="range" min={minWeight} max={maxWeight} step="10" bind:value={weightFilter} />{weightFilter}g
		</div>
		<div>
			<label for="mustHaveUnderseat">Secours sous-cutal :</label>
			<input name="mustHaveUnderseat" type="checkbox" bind:checked={mustHaveUnderseat} />
		</div>
		<div>
			<label for="mustHaveShoulderLinkRescue">Secours relié aux épaules :</label>
			<input name="mustHaveShoulderLinkRescue" type="checkbox" bind:checked={mustHaveShoulderLinkRescue} />
		</div>
	</div>

	<div class="border border-black">
		<canvas bind:this={canvas} />
	</div>
</div>

