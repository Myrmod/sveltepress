<script>
  import Logo from './Logo.svelte'
  import SidebarGroup from './SidebarGroup.svelte'
  import { resolvedSidebar, sidebarCollapsed } from './layout'
  import Close from './icons/Close.svelte'
  import Backdrop from './Backdrop.svelte'
  import { page } from '$app/stores'

  $: routeId = $page.route.id
  $: isHome = routeId === '/'

  const handleClose = () => {
    $sidebarCollapsed = true
  }
</script>

<aside
  class="theme-default-sidebar"
  class:collapsed={$sidebarCollapsed}
  class:is-home={isHome}
>
  <div class="sidebar-logo">
    <Logo />
    <div class="close" on:click={handleClose} on:keyup={handleClose}>
      <Close />
    </div>
  </div>

  {#each $resolvedSidebar as sidebarItem}
    {@const hasItems = Array.isArray(sidebarItem.items)}
    <SidebarGroup
      {...hasItems ? sidebarItem : { title: '', items: [sidebarItem] }}
    />
  {/each}
</aside>

<Backdrop show={!$sidebarCollapsed} on:close={handleClose} />

<style>
  .is-home {
    --at-apply: 'sm:display-none';
  }
  .theme-default-sidebar {
    --at-apply: 'fixed top-0 left-0 bottom-0 pr-6 pb-4 sm:pb-32 overflow-y-auto bg-light-6 dark:bg-dark-9 z-999 sm:w-[25vw] w-[70vw] pl-4 sm:pl-[5vw] md:pl-[10vw] box-border transition-transform transition-300 shadow-md sm:shadow-none';
  }
  .sidebar-logo {
    --at-apply: 'sm:h-[72px] flex items-center justify-between mb-4 py-4 sm:py-0 sticky top-0 bg-inherit border-b-solid border-b border-light-8 dark:border-b-gray-7';
  }
  .collapsed {
    --at-apply: 'sm:translate-x-0';
    transform: translateX(-100%);
  }
  .close {
    --at-apply: 'text-5 flex items-center sm:display-none ml-4';
  }
</style>
