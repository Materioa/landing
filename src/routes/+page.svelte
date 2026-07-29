<script lang="ts">
	import { smoothCorners } from "@lisse/svelte";
	import { ProgressiveBlur } from "$lib/components/magic/progressive-blur";
	import { HugeiconsIcon } from "@hugeicons/svelte";
	import {
		ArrowRight01Icon,
		Compass01Icon,
		Shield01Icon,
		SparklesIcon,
		GlobeIcon,
		Menu01Icon,
		Cancel01Icon,
	} from "@hugeicons/core-free-icons";
	import { fade } from "svelte/transition";
	import { cubicOut } from "svelte/easing";

	function blurSlide(node: Element, { delay = 0, duration = 400, x = 20, blur = 8 }) {
		return {
			delay,
			duration,
			easing: cubicOut,
			css: (t: number, u: number) => `
				transform: translateX(${u * x}px);
				opacity: ${t};
				filter: blur(${u * blur}px);
			`
		};
	}
	let scrollY = $state(0);
	let heroCard = $state<HTMLElement | null>(null);
	let scrolled = $derived(
		heroCard
			? scrollY > heroCard.offsetTop + heroCard.offsetHeight - 20
			: scrollY > 800,
	);

	let mobileMenuOpen = $state(false);
</script>

<svelte:window bind:scrollY />

<!-- SEO Meta Tags -->
<svelte:head>
	<title>Materio</title>
	<meta name="description"
		content="Official materials you trust, in a way you love — Because e-learning shouldn't feel like suffering. Find organized, official study materials, notes, and peer help — all in one clean place." />
	<meta name="robots" content="index, follow">
	<link rel="canonical" href="https://getmaterio.app">
	<meta name="keywords"
		content="Materio, Materio PU, Materio - Home, Materioa, materio, materioa, Parul University, PU, PU Materials, CSE Materials PU, Vadodara, Jinansh Mehta, Materio Github, Materio Study Materials, Study materials, CSE Materials PU">


	<!-- Open Graph Meta Tags -->
	<meta property="og:title" content="Materio">
	<meta property="og:description"
		content="Official materials you trust, in a way you love — Because e-learning shouldn't feel like suffering. Find organized, official study materials, notes, and peer help — all in one clean place." />
	<meta property="og:image" content="https://getmaterio.app/assets/img/open-graph.png">
	<meta property="og:image" content="https://getmaterio.app/assets/img/open-graph.jpeg">
	<meta property="og:image" content="https://getmaterio.app/assets/img/open-graph-general.png">
	<meta property="og:image" content="https://getmaterio.app/assets/img/open-graph-general.jpg">
	<meta property="og:url" content="https://getmaterio.app">
	<meta property="og:type" content="website">

	<!-- Twitter Meta Tags -->
	<meta name="twitter:card" content="summary_large_image">
	<meta name="twitter:title" content="Materio">
	<meta name="twitter:description"
		content="Official materials you trust, in a way you love — Because e-learning shouldn't feel like suffering.">
	<meta name="twitter:image" content="https://getmaterio.app/assets/img/open-graph.png">
	<meta name="theme-color" content="#333333">
</svelte:head>

<ProgressiveBlur
	position="top"
	height="90px"
	class="fixed top-0 left-0 right-0 z-40 pointer-events-none transition-opacity duration-300 {scrolled
		? 'opacity-100'
		: 'opacity-0'}"
/>

<header
	class="fixed top-0 left-0 right-0 z-50 transition-all duration-300 ease-in-out h-16 flex items-center justify-between px-6 md:px-12 bg-cream-50/80 backdrop-blur-md border-b border-cream-200/40 shadow-sm {scrolled
		? 'opacity-100 translate-y-0'
		: 'opacity-0 pointer-events-none -translate-y-2'}"
>
	<a href="/" class="flex items-center group">
		<img
			src="/assets/img/materio_new_bk.svg"
			alt="materio"
			class="h-7 md:h-8 w-auto transition-transform group-hover:scale-[1.01]"
		/>
	</a>

	<nav class="hidden md:flex items-center space-x-8">
		<a
			href="#features"
			class="text-sm font-medium text-neutral-600 hover:text-cream-dark transition-colors"
			>Features</a
		>
		<a
			href="#use-cases"
			class="text-sm font-medium text-neutral-600 hover:text-cream-dark transition-colors"
			>Use cases</a
		>
		<a
			href="#resources"
			class="text-sm font-medium text-neutral-600 hover:text-cream-dark transition-colors"
			>Resources</a
		>
		<a
			href="#pricing"
			class="text-sm font-medium text-neutral-600 hover:text-cream-dark transition-colors"
			>Pricing</a
		>
	</nav>

	<div class="flex items-center">
		<div class="hidden md:block">
			<a
				href="https://getmaterio.app"
				target="_blank"
				use:smoothCorners={{ corners: { radius: 12, smoothing: 0.8 } }}
				class="inline-flex items-center justify-center px-4 py-2 text-xs font-semibold tracking-wide text-cream-50 bg-cream-dark hover:bg-neutral-800 transition-all hover:shadow-md hover:-translate-y-0.5 active:translate-y-0"
			>
				Try Now
			</a>
		</div>
		<button
			onclick={() => mobileMenuOpen = true}
			class="md:hidden text-neutral-800 p-2 -mr-2 transition-opacity hover:opacity-70"
		>
			<HugeiconsIcon icon={Menu01Icon} size={24} />
		</button>
	</div>
</header>

<main
	class="w-full min-h-screen bg-cream-50 p-[calc(var(--spacing)*2)] flex flex-col items-center"
>
	<!-- Giant Hero Card with Sky Background -->
	<div
		bind:this={heroCard}
		use:smoothCorners={{ corners: { radius: 24, smoothing: 0.8 } }}
		class="w-full p-0 mb-16 shadow-xl relative overflow-hidden flex flex-col items-center"
		style="background-image: url('/assets/img/410d0758.webp'); background-size: cover; background-position: center;"
	>
		<!-- Inline Card Header (Scrolls naturally inside the card) -->
		<header
			class="w-full flex items-center justify-between pt-[15px] px-[15px] bg-transparent mb-16 relative z-20"
		>
			<!-- Logo -->
			<a href="/" class="flex items-center group ml-3 md:ml-5">
				<img
					src="/assets/img/materio_new_bk.svg"
					alt="materio"
					class="h-7 md:h-8 w-auto transition-transform group-hover:scale-[1.01]"
				/>
			</a>

			<!-- Navigation -->
			<nav class="hidden md:flex items-center space-x-10">
				<a
					href="#features"
					class="text-[15px] font-semibold text-neutral-800 hover:text-black transition-colors"
					>Features</a
				>
				<a
					href="#use-cases"
					class="text-[15px] font-semibold text-neutral-800 hover:text-black transition-colors"
					>Use cases</a
				>
				<a
					href="#resources"
					class="text-[15px] font-semibold text-neutral-800 hover:text-black transition-colors"
					>Resources</a
				>
				<a
					href="#pricing"
					class="text-[15px] font-semibold text-neutral-800 hover:text-black transition-colors"
					>Pricing</a
				>
			</nav>

			<!-- CTA & Mobile Menu -->
			<div class="flex items-center">
				<div class="hidden md:block">
					<a
						href="https://getmaterio.app"
						target="_blank"
						use:smoothCorners={{ corners: { radius: 14, smoothing: 0.8 } }}
						class="inline-flex items-center justify-center px-5 py-2.5 text-sm font-semibold tracking-wide text-cream-50 bg-cream-dark hover:bg-neutral-800 transition-all hover:shadow-md hover:-translate-y-0.5 active:translate-y-0"
					>
						Try Now
					</a>
				</div>
				<button
					onclick={() => mobileMenuOpen = true}
					class="md:hidden text-neutral-800 p-2 -mr-2 transition-opacity hover:opacity-70"
				>
					<HugeiconsIcon icon={Menu01Icon} size={24} />
				</button>
			</div>
		</header>

		<!-- Hero Content inside Card -->
		<div
			class="flex flex-col items-start text-left md:items-center md:text-center relative z-20 max-w-4xl px-[15px] md:px-4 w-full"
		>
			<!-- Main Title -->
			<h1
				class="font-sans text-3xl md:text-5xl font-normal tracking-tight text-cream-dark leading-[1.1] mb-6"
			>
				Everything you've learned.<br class="hidden sm:inline" /> Instantly
				accessible.
			</h1>
		</div>

		<!-- Mock Browser Window (Screenshot Container) -->
		<!-- Overflowing on mobile: fixed width pulls it off the right edge, negative margin clips bottom -->
		<div
			class="self-start md:self-center rounded-t-[20px] w-[800px] max-w-none md:w-[calc(100%-96px)] md:max-w-full ml-[15px] md:ml-0 mt-8 md:mt-12 h-[320px] sm:h-[450px] md:h-[550px] bg-cream-100/90 backdrop-blur-xl border-t border-x border-cream-200/40 shadow-[0_20px_50px_rgba(27,23,19,0.06)] flex flex-col relative z-10 overflow-hidden mb-[-20px] md:mb-0"
		>
			<!-- Browser Header/Toolbar -->
			<div
				class="h-11 px-4 border-b border-cream-200/40 flex items-center justify-between bg-cream-100/40 backdrop-blur-sm"
			>
				<!-- Traffic Light Controls -->
				<div class="flex items-center space-x-1.5 w-1/4">
					<div class="w-3.5 h-3.5 rounded-full bg-red-400/80"></div>
					<div
						class="w-3.5 h-3.5 rounded-full bg-yellow-400/80"
					></div>
					<div class="w-3.5 h-3.5 rounded-full bg-green-400/80"></div>
				</div>

				<!-- Address Bar -->
				<div
					class="w-2/4 max-w-md h-7 rounded-lg bg-cream-200/55 border border-cream-200/40 px-3 flex items-center space-x-2 text-neutral-500 text-xs"
				>
					<HugeiconsIcon
						icon={GlobeIcon}
						size={14}
						class="text-neutral-400"
					/>
					<span class="truncate font-sans font-medium tracking-tight"
						>https://getmaterio.app</span
					>
				</div>

				<!-- Extra Actions -->
				<div
					class="w-1/4 flex items-center justify-end space-x-3 text-neutral-400"
				>
					<div
						class="w-4 h-4 rounded-md border border-cream-300/60 bg-cream-200/30"
					></div>
				</div>
			</div>

			<div class="flex-grow bg-cream-50/10 relative">
				<img
					src="/assets/img/b7b2829d.webp"
					alt="Materio Workspace"
					class="w-full h-full object-cover object-top"
				/>
			</div>
		</div>
	</div>

	<!-- Info Grid (Below the Hero Card) -->
	<div
		class="w-full max-w-5xl grid grid-cols-1 md:grid-cols-12 gap-8 pt-6 border-t border-cream-200 mb-24"
	>
		<div class="md:col-span-4 flex flex-col">
			<a
				href="#introducing"
				class="inline-flex items-center space-x-1.5 text-xs font-semibold text-brand-600 hover:text-brand-700 transition-colors uppercase tracking-wider mb-2"
			>
				<span>Introducing Materio</span>
				<HugeiconsIcon icon={ArrowRight01Icon} size={14} />
			</a>
		</div>
		<div class="md:col-span-8 flex flex-col space-y-6">
			<p
				class="font-sans text-lg md:text-xl font-normal leading-relaxed text-neutral-500"
			>
				<span class="text-cream-dark font-semibold"
					>Today's AI browsers are broken.</span
				> They never complete a task. They say "I can't do this" every time.
				They stop every time the work gets real, and they still lack the
				basics: speed and usability.
			</p>
			<p
				class="font-sans text-lg md:text-xl font-normal leading-relaxed text-neutral-500"
			>
				<span class="text-cream-dark font-semibold"
					>Materio is a browser rebuilt for people and agents.</span
				> It works across your logged-in websites and handles complex work
				other agents can't: messages, payments, internal tools, and everything
				in between.
			</p>
		</div>
	</div>

	<!-- Features Grid Section -->
	<section id="features" class="w-full max-w-5xl mb-32">
		<div class="text-center md:text-left mb-16">
			<h2
				class="text-xs font-bold text-neutral-400 uppercase tracking-widest mb-3"
			>
				Key Capabilities
			</h2>
			<p class="text-3xl font-semibold tracking-tight text-cream-dark">
				Designed for the agentic age.
			</p>
		</div>

		<div class="grid grid-cols-1 md:grid-cols-3 gap-6">
			<!-- Feature 1 -->
			<div
				class="p-8 rounded-3xl bg-cream-100/50 border border-cream-200/50 hover:border-cream-300 hover:shadow-md transition-all duration-300"
			>
				<div
					class="w-10 h-10 rounded-xl bg-cream-200 flex items-center justify-center text-cream-dark mb-6"
				>
					<HugeiconsIcon icon={SparklesIcon} size={20} />
				</div>
				<h3 class="font-semibold text-base text-cream-dark mb-2">
					Zero-Trust Agency
				</h3>
				<p class="text-sm leading-relaxed text-neutral-500">
					Secure execution of agents on top of your existing browser
					sessions, preserving privacy.
				</p>
			</div>

			<!-- Feature 2 -->
			<div
				class="p-8 rounded-3xl bg-cream-100/50 border border-cream-200/50 hover:border-cream-300 hover:shadow-md transition-all duration-300"
			>
				<div
					class="w-10 h-10 rounded-xl bg-cream-200 flex items-center justify-center text-cream-dark mb-6"
				>
					<HugeiconsIcon icon={Compass01Icon} size={20} />
				</div>
				<h3 class="font-semibold text-base text-cream-dark mb-2">
					Native DOM Control
				</h3>
				<p class="text-sm leading-relaxed text-neutral-500">
					Our custom rendering pipeline allows AI agents to interact
					with elements just like human cursors do.
				</p>
			</div>

			<!-- Feature 3 -->
			<div
				class="p-8 rounded-3xl bg-cream-100/50 border border-cream-200/50 hover:border-cream-300 hover:shadow-md transition-all duration-300"
			>
				<div
					class="w-10 h-10 rounded-xl bg-cream-200 flex items-center justify-center text-cream-dark mb-6"
				>
					<HugeiconsIcon icon={Shield01Icon} size={20} />
				</div>
				<h3 class="font-semibold text-base text-cream-dark mb-2">
					Built-in Authentication
				</h3>
				<p class="text-sm leading-relaxed text-neutral-500">
					Maintains secure authorization so agents can operate
					natively inside complex dashboards.
				</p>
			</div>
		</div>
	</section>
</main>

<footer
	class="w-full py-12 px-6 md:px-12 border-t border-cream-200/60 max-w-7xl mx-auto flex flex-col md:flex-row items-center justify-between text-neutral-400 text-xs"
>
	<div class="flex items-center space-x-2 mb-4 md:mb-0">
		<span class="font-semibold text-neutral-700">materio</span>
		<span>© 2026. All rights reserved.</span>
	</div>
	<div class="flex space-x-6">
		<a href="#privacy" class="hover:text-neutral-600 transition-colors"
			>Privacy Policy</a
		>
		<a href="#terms" class="hover:text-neutral-600 transition-colors"
			>Terms of Service</a
		>
		<a href="#contact" class="hover:text-neutral-600 transition-colors"
			>Contact</a
		>
	</div>
</footer>

<!-- Mobile Menu Overlay -->
{#if mobileMenuOpen}
	<!-- svelte-ignore a11y_no_static_element_interactions -->
	<div
		transition:fade={{ duration: 250 }}
		class="fixed inset-0 z-[100] bg-cream-50/95 backdrop-blur-xl overflow-y-auto flex flex-col"
	>
		<!-- Header -->
		<div class="flex items-center justify-between p-6 pt-8 pb-4">
			<!-- Logo -->
			<a href="/" class="flex items-center ml-1" onclick={() => mobileMenuOpen = false}>
				<img
					src="/assets/img/materio_new_bk.svg"
					alt="materio"
					class="h-7 w-auto"
				/>
			</a>
			<!-- Close Button -->
			<button
				onclick={() => mobileMenuOpen = false}
				class="p-2 -mr-2 text-neutral-800 transition-opacity hover:opacity-70"
			>
				<HugeiconsIcon icon={Cancel01Icon} size={24} />
			</button>
		</div>

		<!-- Links -->
		<div class="flex flex-col mt-4">
			<a
				href="#features"
				onclick={() => mobileMenuOpen = false}
				in:blurSlide={{ delay: 100 }} out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Features
			</a>
			<a
				href="#use-cases"
				onclick={() => mobileMenuOpen = false}
				in:blurSlide={{ delay: 150 }} out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Use cases
			</a>
			<a
				href="#resources"
				onclick={() => mobileMenuOpen = false}
				in:blurSlide={{ delay: 200 }} out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Resources
			</a>
			<a
				href="#pricing"
				onclick={() => mobileMenuOpen = false}
				in:blurSlide={{ delay: 250 }} out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Pricing
			</a>
			<a
				href="https://getmaterio.app"
				target="_blank"
				onclick={() => mobileMenuOpen = false}
				in:blurSlide={{ delay: 300 }} out:fade={{ duration: 150 }}
				class="py-6 px-7 border-y border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Try Now
			</a>
		</div>
	</div>
{/if}

<style>
	/* Subtle transition styles */
	:global(html) {
		scroll-behavior: smooth;
	}

</style>
