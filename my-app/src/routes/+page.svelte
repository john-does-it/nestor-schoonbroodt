<script lang="ts">
  let name = $state('Jonathan')
  let test = 'test'
</script>

{#each examples as example}
  {#if example}
    {example}
  {/if}
{/each}
