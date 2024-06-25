<script lang="ts">
	import type { CartProduct } from '$lib/types';
	import CartItem from '$lib/CartItem.svelte';
	import { ShoppingCart } from 'lucide-svelte';
	import { X } from 'lucide-svelte';

	let { data } = $props();

	let cartOpen = $state(false);
	let cartProducts = $state<CartProduct[]>([]);

	const cartStats = $derived.by(() => {
		let quantity = 0;
		let total = 0;
		for (const product of cartProducts) {
			quantity += product.quantity;
			total += product.product.price * product.quantity;
		}
		return {
			quantity,
			total
		};
	});

	const qualifiesForFreeShipping = $derived(cartStats.total >= 50);

	// let freeShippingAlertCount = 0;

	$effect(() => {
		// if (freeShippingAlertCount > 0) return;
		if (qualifiesForFreeShipping) {
			alert('50 ₺ üzeri ücretsiz kargo hakkı kazandınız!');
			// freeShippingAlertCount++;
		}
	});

	function removeFromCart(id: string) {
		cartProducts = cartProducts.filter((product) => product.id !== id);
	}
</script>

<div class="flex items-center p-4">
	<span class="text-lg font-bold">3DPartnerim</span>
	<div class="relative ml-auto flex items-center">
		<button onclick={() => (cartOpen = !cartOpen)} class="btn flex items-center preset-filled">
			<ShoppingCart class="size-5" />
			<span>{cartStats.quantity} ürün</span>
		</button>
		{#if cartOpen}
			<div class="absolute right-0 top-8 z-10 mt-2 w-80 rounded-lg bg-white text-black shadow-xl">
				<div class="relative p-4">
					<h2 class="mb-4 text-lg font-semibold">Sepetim</h2>
					<button
						class="absolute right-4 top-4 rounded-full p-1 hover:bg-gray-100"
						aria-label="close cart"
						onclick={() => (cartOpen = false)}
					>
						<X class="size-4" />
					</button>
					{#each cartProducts as _, i}
						<CartItem bind:cartProduct={cartProducts[i]} removeItem={removeFromCart} />
					{/each}
					<div class="mt-4 border-gray-200 pt-4">
						<p class="text-lg font-semibold">Toplam: {cartStats.total.toFixed(2)} ₺</p>
					</div>
				</div>
			</div>
		{/if}
	</div>
</div>

<div class="grid grid-cols-1 gap-6 p-8 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4">
	{#each data.products as product}
		<div class="overflow-hidden rounded-xl shadow-lg">
			<img src={product.thumbnail} alt={product.title} class="h-48 w-full object-cover" />
			<div class="p-4">
				<p class="mb-2 overflow-hidden truncate text-lg font-medium">
					{product.title}
				</p>
				<div class="flex items-center justify-between">
					<p class="text-xl font-bold">{product.price} ₺</p>
					<button
						class="btn px-4 py-2 text-white transition-colors duration-300 preset-filled-success-50-950"
						onclick={() => {
							cartProducts.push({
								id: crypto.randomUUID(),
								quantity: 1,
								product: product
							});
						}}
					>
						Sepete ekle
					</button>
				</div>
			</div>
		</div>
	{/each}
</div>
