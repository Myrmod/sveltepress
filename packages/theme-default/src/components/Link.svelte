<script>
  import External from './icons/External.svelte'
  export let label = ''
  export let to = ''
  export let inline = true
  export let active = false
  export let highlight = true

  $: isExternal = /^https?/.test(to)
</script>

<a
  href={to}
  class="link"
  class:no-inline={!inline}
  class:active
  class:highlight
  {...isExternal ? { target: '_blank' } : {}}
>
  <slot name="pre" />
  <div>
    {label}
  </div>
  {#if isExternal}
    <External />
  {/if}
  <slot />
</a>

<style>
  .highlight {
    --at-apply: text-svp-primary;
  }
  .link {
    --at-apply: 'inline-flex hover:text-svp-hover cursor-pointer items-center transition-200 transition-color';
  }
  .link.no-inline {
    --at-apply: 'flex';
  }
  .active {
    --at-apply: 'svp-gradient-text hover:svp-gradient-text cursor-default';
  }
  :global(.admonition .highlight) {
    --uno: 'text-[var(--admonition-color)]';
  }
  :global(.admonition .link) {
    --uno: 'hover:text-[var(--admonition-color)] hover:opacity-80';
  }
</style>
