<script lang="ts">
	// Types
	import type { ModalSettings, ModalComponent } from '$lib/utilities/Modal/types';
	import type { DrawerSettings } from '$lib/utilities/Drawer/types';

	// Docs
	import DocsLogoFull from '$docs/DocsLogo/DocsLogoFull.svelte';
	import DocsLogoIcon from '$docs/DocsLogo/DocsLogoIcon.svelte';
	import DocsSearch from '$docs/DocsSearch/DocsSearch.svelte';

	// Components
	import AppBar from '$lib/components/AppBar/AppBar.svelte';
	import Divider from '$lib/components/Divider/Divider.svelte';
	import SvgIcon from '$lib/components/SvgIcon/SvgIcon.svelte';
	// Utilities
	import LightSwitch from '$lib/utilities/LightSwitch/LightSwitch.svelte';
	import { menu } from '$lib/utilities/Menu/menu';
	import { modalStore } from '$lib/utilities/Modal/stores';

	// Stores
	import { storeTheme } from '$docs/stores';
	import { drawerStore } from '$lib/utilities/Drawer/stores';

	// Drawer Handler
	function drawerOpen(): void {
		const s: DrawerSettings = { id: 'doc-sidenav' };
		drawerStore.open(s);
	}

	// Search
	function search(): void {
		const modalComponent: ModalComponent = { ref: DocsSearch };
		const d: ModalSettings = {
			type: 'component',
			component: modalComponent,
			classes: '!p-0'
		};
		modalStore.trigger(d);
	}

	// Keyboard Shortcut (⌘+K) to Focus Search
	let pressedKeys: string[] = [];
	function onWindowKeydown(e: any): void {
		if ($modalStore.length) return;
		if (e.code === 'MetaLeft' || e.code === 'KeyK') {
			// Set pressed keys
			pressedKeys = [...pressedKeys, e.code];
			// If both keys pressed, focus input
			if (pressedKeys.includes('MetaLeft') && pressedKeys.includes('KeyK')) search();
		}
	}
	function onWindowKeyup(): void {
		pressedKeys = [];
	}
</script>

<!-- NOTE: using stopPropagation to override Chrome for Windows search shortcut -->
<svelte:window on:keydown|stopPropagation={onWindowKeydown} on:keyup={onWindowKeyup} />

<AppBar>
	<!-- Branding -->
	<svelte:fragment slot="lead">
		<!-- Drawer Menu -->
		<button on:click={drawerOpen} class="lg:!hidden btn btn-sm">
			<SvgIcon name="bars" />
		</button>
		<!-- Logo -->
		<a class="mr-4" href="/" title="Go to Homepage">
			<span class="hidden sm:inline"><DocsLogoFull /></span>
			<span class="inline sm:hidden"><DocsLogoIcon /></span>
		</a>
	</svelte:fragment>

	<!-- Search -->
	<div class="hidden md:inline">
		<button class="btn btn-ghost-surface btn-sm" on:click={search}>
			<SvgIcon name="search" width="w-4" height="h-4" class="mr-2" />
			<span>Search</span>
			<!-- <span>⌘K</span> -->
		</button>
	</div>

	<!-- Navigation -->
	<svelte:fragment slot="trail">
		<!-- Links -->
		<!-- prettier-ignore -->
		<section class="hidden lg:flex">
			<!-- Docs -->
			<a class="unstyled hover:bg-primary-hover-token px-4 py-2 rounded-token" href="/docs/why" data-sveltekit-preload-data="hover">Docs</a>
			<!-- Guides -->
			<a class="unstyled hover:bg-primary-hover-token px-4 py-2 rounded-token" href="/guides/install" data-sveltekit-preload-data="hover">Guides</a>
			<!-- Features -->
			<div class="relative">
				<button class="unstyled hover:bg-primary-hover-token px-4 py-2 rounded-token space-x-2" use:menu={{ menu: 'features' }}>
					<span>Features</span>
					<span class="opacity-50">▾</span>
				</button>
				<div class="card overflow-hidden w-60 shadow-xl grid grid-cols-1" data-menu="features">
					<!-- Tailwind -->
					<a class="grid grid-cols-[auto_1fr] gap-4 p-4 hover:bg-primary-hover-token" href="/elements/core" data-sveltekit-preload-data="hover">
						<div class="flex justify-center items-center">
							<SvgIcon name="tailwind" />
						</div>
						<div>
							<h4>Tailwind</h4>
							<small>Elements styled by Tailwind.</small>
						</div>
					</a>
					<hr>
					<!-- Svelte -->
					<a class="grid grid-cols-[auto_1fr] gap-4 p-4 hover:bg-primary-hover-token" href="/actions/clipboard" data-sveltekit-preload-data="hover">
						<div class="flex justify-center items-center">
							<SvgIcon name="svelte" />
						</div>
						<div>
							<h4>Svelte</h4>
							<small>Actions and Components.</small>
						</div>
					</a>
					<hr>
					<!-- Utilities -->
					<a class="grid grid-cols-[auto_1fr] gap-4 p-4 hover:bg-primary-hover-token" href="/utilities/codeblocks" data-sveltekit-preload-data="hover">
						<div class="flex justify-center items-center">
							<SvgIcon name="screwdriver" />
						</div>
						<div>
							<h4>Utilities</h4>
							<small>Powerful utility features.</small>
						</div>
					</a>
				</div>
			</div>
			<!-- Blog -->
			<a class="unstyled hover:bg-primary-hover-token px-4 py-2 rounded-token" href="/blog" data-sveltekit-preload-data="hover">Blog</a>
		</section>

		<Divider vertical borderWidth="hidden lg:block border-l-2 opacity-20" />

		<!-- Theme -->
		<!-- prettier-ignore -->
		<div class="relative">
			<button class="unstyled hover:bg-primary-hover-token px-4 py-2 rounded-token space-x-2" use:menu={{ menu: 'theme', interactive: true }}>
				<SvgIcon name="swatchbook" width="w-4" height="w-4" class="inline-block md:hidden" />
				<span class="hidden md:inline-block">Theme</span>
				<span class="opacity-50">▾</span>
			</button>
			<div class="card w-56 shadow-xl" data-menu="theme">
				<section class="flex justify-between items-center p-4">
					<h6>Theme</h6>
					<LightSwitch />
				</section>
				<hr>
				<nav class="list-nav p-4 max-h-64 overflow-y-auto">
					<ul>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'skeleton'} on:click={() => { storeTheme.set('skeleton') }} on:keypress> 
							<span>💀</span>
							<span>Skeleton</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'modern'} on:click={() => { storeTheme.set('modern') }} on:keypress>
							<span>🤖</span>
							<span>Modern</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'rocket'} on:click={() => { storeTheme.set('rocket') }} on:keypress> 
							<span>🚀</span>
							<span>Rocket</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'seafoam'} on:click={() => { storeTheme.set('seafoam') }} on:keypress>
							<span>🐚</span>
							<span>Seafoam</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'vintage'} on:click={() => { storeTheme.set('vintage') }} on:keypress>
							<span>📺</span>
							<span>Vintage</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'sahara'} on:click={() => { storeTheme.set('sahara') }} on:keypress>
							<span>🏜️</span>
							<span>Sahara</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'hamlindigo'} on:click={() => { storeTheme.set('hamlindigo') }} on:keypress>
							<span>👔</span>
							<span>Hamlindigo</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'goldNouveau'} on:click={() => { storeTheme.set('goldNouveau') }} on:keypress>
							<span>💫</span>
							<span>Gold Nouveau</span>
						</li>
						<li class="option" class:bg-primary-active-token={$storeTheme === 'crimson'} on:click={() => { storeTheme.set('crimson') }} on:keypress>
							<span>⭕</span>
							<span>Crimson</span>
						</li>
						<!-- <li class="option" class:bg-primary-active-token={$storeTheme === 'test'} on:click={() => { storeTheme.set('test') }} on:keypress>
							<span>🚧</span>
							<span>Test</span>
						</li> -->
						<!-- <li class="option" class:bg-primary-active-token={$storeTheme === 'seasonal'} on:click={() => { storeTheme.set('seasonal') }} on:keypress>
							<span>🎃</span>
							<span>Seasonal</span>
						</li> -->
					</ul>
				</nav>
				<hr>
				<div class="p-4">
					<a class="btn btn-ghost-surface w-full" href="/guides/themes/generator">Theme Generator</a>
				</div>
			</div>
		</div>

		<Divider vertical borderWidth="border-l-2 opacity-20" />

		<!-- Social -->
		<section class="flex">
			<a class="btn btn-sm" href="https://discord.gg/EXqV7W8MtY" target="_blank" rel="noreferrer" aria-label="Discord">
				<SvgIcon name="discord" viewBox="0 0 640 512" />
			</a>
			<a class="btn btn-sm" href="https://twitter.com/SkeletonUI" target="_blank" rel="noreferrer" aria-label="Twitter">
				<SvgIcon name="twitter" />
			</a>
			<a class="btn btn-sm" href="https://github.com/skeletonlabs/skeleton" target="_blank" rel="noreferrer" aria-label="GitHub">
				<SvgIcon name="github" />
			</a>
		</section>
	</svelte:fragment>
</AppBar>
