<script>
  import { tick } from 'svelte'
  import siteConfig from 'virtual:sveltepress/site'
  import themeOptions from 'virtual:sveltepress/theme-default'
  import Home from './Home.svelte'
  import PageSwitcher from './PageSwitcher.svelte'
  import EditPage from './EditPage.svelte'
  import LastUpdate from './LastUpdate.svelte'
  import { anchors, pages } from './layout'
  import HeroImage from './home/HeroImage.svelte'
  import { page } from '$app/stores'
  import { afterNavigate, beforeNavigate } from '$app/navigation'

  const routeId = $page.route.id

  // The frontmatter info. This would be injected by sveltepress
  export let fm = {}

  const {
    title,
    description,
    pageType,
    lastUpdate,
    anchors: fmAnchors = [],
    home,
  } = fm

  const isHome = routeId === '/'

  anchors.set(fmAnchors)

  let ready = false

  beforeNavigate(() => {
    ready = false
  })

  afterNavigate(() => {
    tick().then(() => {
      ready = true
    })
  })
</script>

<svelte:head>
  <title>{title ? `${title} - ${siteConfig.title}` : siteConfig.title}</title>
  <meta name="description" content={description || siteConfig.description} />
</svelte:head>

{#if !isHome}
  <div pb-4 class="theme-default--page-layout">
    <div class="content">
      {#if title}
        <h1 class="page-title">
          {title}
        </h1>
      {/if}
      <slot />
      <div class="meta" class:without-edit-link={!themeOptions.editLink}>
        {#if themeOptions.editLink}
          <EditPage {pageType} />
        {/if}
        <LastUpdate {lastUpdate} />
      </div>
      {#if ready && $pages.length}
        <PageSwitcher />
      {/if}
    </div>
  </div>
{:else}
  {#if home !== false}
    <Home {...fm} {siteConfig}>
      <slot name="hero-image" slot="hero-image">
        <HeroImage heroImage={fm.heroImage} />
      </slot>
    </Home>
  {/if}
  <slot />
{/if}

<style>
  :global(.theme-default--page-layout h2 .svp-title-anchor),
  :global(.theme-default--page-layout h3 .svp-title-anchor),
  :global(.theme-default--page-layout h4 .svp-title-anchor),
  :global(.theme-default--page-layout h5 .svp-title-anchor),
  :global(.theme-default--page-layout h6 .svp-title-anchor) {
    --at-apply: 'absolute left-0 top-[50%] flex items-center opacity-0 pointer-events-none hover:text-svp-hover transition-all transition-200';
    transform: translate(-100%, -50%);
  }

  :global(.theme-default--page-layout h2 .svp-title-anchor) {
    transform: translate(-100%, calc((-100% + 1rem) / 2));
  }
  :global(.theme-default--page-layout h2),
  :global(.theme-default--page-layout h3),
  :global(.theme-default--page-layout h4),
  :global(.theme-default--page-layout h5),
  :global(.theme-default--page-layout h6) {
    --at-apply: relative;
  }
  :global(.theme-default--page-layout h2) {
    --at-apply: 'border-t-solid border-t border-light-7 dark:border-gray-7 pt-4 mt-8 mb-4';
  }
  :global(.theme-default--page-layout h2:hover .svp-title-anchor),
  :global(.theme-default--page-layout h3:hover .svp-title-anchor),
  :global(.theme-default--page-layout h4:hover .svp-title-anchor),
  :global(.theme-default--page-layout h5:hover .svp-title-anchor),
  :global(.theme-default--page-layout h6:hover .svp-title-anchor) {
    --at-apply: pointer-events-auto opacity-100;
  }
  .content {
    --at-apply: 'sm:w-[45vw] mx-auto pb-8 sm:pb-28 w-[90vw]';
  }
  .page-title {
    --at-apply: mt-none;
  }
  .meta {
    --at-apply: 'sm:flex justify-between mt-20 column';
  }
  .without-edit-link {
    --at-apply: 'justify-end';
  }
</style>
