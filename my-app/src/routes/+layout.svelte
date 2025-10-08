<script lang="ts">
  import favicon from '$lib/assets/favicon.svg'
	import { dev } from '$app/environment'
	import { injectAnalytics } from '@vercel/analytics/sveltekit'

	injectAnalytics({ mode: dev ? 'development' : 'production' })
  
  let { children } = $props()	
</script>

<svelte:head>
  <link rel="icon" href={favicon} />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
</svelte:head>

{@render children?.()}
