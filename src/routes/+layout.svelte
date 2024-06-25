<script>
	import '../app.css';
	let { children } = $props();
</script>

<svelte:head>
	<title>3DPartnerim | 3D Printing Solutions</title>
	<meta name="description" content="3D Printing Solutions…" />
	<meta name="keywords" content="3D, Model, Print" />
	<meta name="author" content="Ercode" />
</svelte:head>

<!-- <slot /> -->
{@render children?.()}
