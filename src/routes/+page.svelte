<script>
	import { products } from '$lib/data/products';
	import { cart } from '$lib/stores/cart';
	import { toasts } from '$lib/stores/toast';
	import { page } from '$app/stores';
	import { getCategoryKey } from '$lib/utils/categoryUtils';

	$: category = $page.url.searchParams.get('category') || 'TVs';
	$: currentProducts = products[getCategoryKey(category)] || [];

	function addToCart(product) {
		cart.update((items) => [...items, product]);
		toasts.add(`${product.name} added to cart`);
		
		// Send event to server with product details
		window.send_page_control_event({
			action: 'add_to_cart',
			product: {
				id: product.id,
				name: product.name,
				price: product.price,
				category: product.category
			}
		});
	}
</script>

<div class="space-y-8">
	<h1 class="text-3xl font-bold text-gray-900">{category}</h1>

	<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
		{#each currentProducts as product}
			<div class="bg-white rounded-lg shadow-md overflow-hidden">
				<img
					src={product.image}
					alt={product.name}
					class="w-full h-48 object-cover"
				/>
				<div class="p-4 space-y-2">
					<h2 class="text-xl font-semibold text-gray-800">{product.name}</h2>
					<p class="text-gray-600">${product.price.toFixed(2)}</p>
					<button
						on:click={() => addToCart(product)}
						class="w-full bg-blue-600 text-white py-2 px-4 rounded-md hover:bg-blue-700 transition-colors"
					>
						Add to Cart
					</button>
				</div>
			</div>
		{/each}
	</div>
</div>