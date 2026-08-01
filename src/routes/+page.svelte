<script lang="ts">
	import { onMount } from "svelte";
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
		HeartIcon,
		GoogleDriveIcon,
		WhatsappIcon,
		UniversityIcon,
		Download01Icon,
		Bookmark01Icon,
	} from "@hugeicons/core-free-icons";
	import { fade } from "svelte/transition";
	import { cubicOut } from "svelte/easing";

	function blurSlide(
		node: Element,
		{ delay = 0, duration = 400, x = 20, blur = 8 },
	) {
		return {
			delay,
			duration,
			easing: cubicOut,
			css: (t: number, u: number) => `
				transform: translateX(${u * x}px);
				opacity: ${t};
				filter: blur(${u * blur}px);
			`,
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

	let statusText = $state("All systems operational");
	let statusType = $state("operational");

	interface Post {
		title: string;
		excerpt: string;
		category?: string;
		slug?: string;
		date?: string;
	}

	let posts = $state<Post[]>([]);

	function formatDate(dateStr?: string): string {
		if (!dateStr) return "";
		try {
			const cleanStr = dateStr.split(" ")[0];
			const date = new Date(cleanStr);
			if (isNaN(date.getTime())) return dateStr;
			return date.toLocaleDateString("en-US", {
				month: "long",
				day: "numeric",
				year: "numeric",
			});
		} catch (e) {
			return dateStr;
		}
	}

	onMount(async () => {
		try {
			const res = await fetch(
				"https://getmaterio.app/api/v2/features?action=posts&category=whats-new",
			);
			if (res.ok) {
				const data = await res.json();
				if (Array.isArray(data) && data.length > 0) {
					posts = data.slice(0, 2);
				}
			}
		} catch (e) {
			console.error("Failed to load posts:", e);
		}

		try {
			const statusRes = await fetch(
				"https://status.getmaterio.app/api/v1/summary",
			);
			if (statusRes.ok) {
				const statusData = await statusRes.json();
				if (
					statusData.ongoing_incidents &&
					statusData.ongoing_incidents.length > 0
				) {
					statusText = "Degraded performance";
					statusType = "degraded";
				} else if (
					statusData.in_progress_maintenances &&
					statusData.in_progress_maintenances.length > 0
				) {
					statusText = "Maintenance in progress";
					statusType = "degraded";
				} else {
					statusText = "All systems operational";
					statusType = "operational";
				}
			} else {
				statusText = "Status unknown";
				statusType = "degraded";
			}
		} catch (e) {
			console.error("Failed to load status:", e);
			statusText = "Status unknown";
			statusType = "degraded";
		}
	});

	let imageErrors = $state<Record<string, boolean>>({});
	const connectorLogos = [
		{
			name: "ChatGPT",
			img: "/assets/img/chatgpt-icon.svg",
			bg: "bg-white",
			border: "border-emerald-200/55",
			textColor: "text-emerald-700",
			logoColor: "#10a37f",
		},
		{
			name: "Claude",
			img: "/assets/img/claude-ai-icon.svg",
			bg: "bg-white",
			border: "border-orange-200/55",
			textColor: "text-orange-700",
			logoColor: "#ea825a",
		},
		{
			name: "Perplexity",
			img: "/assets/img/perplexity-ai-icon.svg",
			bg: "bg-white",
			border: "border-teal-200/55",
			textColor: "text-teal-700",
			logoColor: "#22808d",
		},
		{
			name: "Notion",
			img: "/assets/img/notion-icon.svg",
			bg: "bg-white",
			border: "border-zinc-300/50",
			textColor: "text-zinc-800",
			logoColor: "#000000",
		},
		{
			name: "Poke",
			img: "/assets/img/poke-ai-icon.webp",
			bg: "bg-white",
			border: "border-blue-200/55",
			textColor: "text-blue-700",
			logoColor: "#4f46e5",
		},
		{
			name: "Thinklet",
			img: "/assets/img/thinklet-ai-icon.svg",
			bg: "bg-white",
			border: "border-purple-200/55",
			textColor: "text-purple-700",
			logoColor: "#7c3aed",
		},
	];
</script>

<svelte:window bind:scrollY />

<!-- SEO Meta Tags -->
<svelte:head>
	<title>Materio</title>
	<meta
		name="description"
		content="Official materials you trust, in a way you love — Because e-learning shouldn't feel like suffering. Find organized, official study materials, notes, and peer help — all in one clean place."
	/>
	<meta name="robots" content="index, follow" />
	<link rel="canonical" href="https://getmaterio.app" />
	<meta
		name="keywords"
		content="Materio, Materio PU, Materio - Home, Materioa, materio, materioa, Parul University, PU, PU Materials, CSE Materials PU, Vadodara, Jinansh Mehta, Materio Github, Materio Study Materials, Study materials, CSE Materials PU"
	/>

	<!-- Open Graph Meta Tags -->
	<meta property="og:title" content="Materio" />
	<meta
		property="og:description"
		content="Official materials you trust, in a way you love — Because e-learning shouldn't feel like suffering. Find organized, official study materials, notes, and peer help — all in one clean place."
	/>
	<meta
		property="og:image"
		content="https://getmaterio.app/assets/img/open-graph.png"
	/>
	<meta
		property="og:image"
		content="https://getmaterio.app/assets/img/open-graph.jpeg"
	/>
	<meta
		property="og:image"
		content="https://getmaterio.app/assets/img/open-graph-general.png"
	/>
	<meta
		property="og:image"
		content="https://getmaterio.app/assets/img/open-graph-general.jpg"
	/>
	<meta property="og:url" content="https://getmaterio.app" />
	<meta property="og:type" content="website" />

	<!-- Twitter Meta Tags -->
	<meta name="twitter:card" content="summary_large_image" />
	<meta name="twitter:title" content="Materio" />
	<meta
		name="twitter:description"
		content="Official materials you trust, in a way you love — Because e-learning shouldn't feel like suffering."
	/>
	<meta
		name="twitter:image"
		content="https://getmaterio.app/assets/img/open-graph.png"
	/>
	<meta name="theme-color" content="#333333" />
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
			href="#connectors"
			class="text-sm font-medium text-neutral-600 hover:text-cream-dark transition-colors"
			>Connectors</a
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
			onclick={() => (mobileMenuOpen = true)}
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
		style="background-image: url('/assets/img/0a981100.webp'); background-size: cover; background-position: center;"
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
					href="#connectors"
					class="text-[15px] font-semibold text-neutral-800 hover:text-black transition-colors"
					>Connectors</a
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
						use:smoothCorners={{
							corners: { radius: 14, smoothing: 0.8 },
						}}
						class="inline-flex items-center justify-center px-5 py-2.5 text-sm font-semibold tracking-wide text-cream-50 bg-cream-dark hover:bg-neutral-800 transition-all hover:shadow-md hover:-translate-y-0.5 active:translate-y-0"
					>
						Try Now
					</a>
				</div>
				<button
					onclick={() => (mobileMenuOpen = true)}
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
				class="font-sans text-3xl md:text-5xl font-bold tracking-tight text-cream-dark leading-[1.1] mb-6"
			>
				Everything you've learned.<br class="hidden sm:inline" /> Instantly
				accessible.
			</h1>
		</div>

		<!-- Mock Browser Window (Screenshot Container) -->
		<!-- Overflowing on mobile: fixed width pulls it off the right edge, negative margin clips bottom -->
		<div
			class="self-start md:self-center rounded-t-xl w-[800px] max-w-none md:w-[calc(100%-96px)] md:max-w-full ml-[15px] md:ml-0 mt-8 md:mt-12 h-[320px] sm:h-[450px] md:h-[550px] bg-cream-100/90 backdrop-blur-xl border-t border-x border-cream-200/40 shadow-[0_20px_50px_oklch(0.2079_0.01_67.2_/_0.06)] flex flex-col relative z-10 overflow-hidden mb-[-20px] md:mb-0"
		>
			<!-- Browser Header/Toolbar -->
			<div
				class="h-[40px] px-4 border-b border-cream-200/40 flex items-center justify-between bg-cream-100/60 backdrop-blur-sm select-none"
			>
				<!-- Left side: Traffic Lights & Controls -->
				<div class="flex items-center space-x-3.5">
					<!-- Traffic Light Controls -->
					<div class="flex items-center space-x-1.5">
						<div class="w-3 h-3 rounded-full bg-[#EC6A5E]"></div>
						<div class="w-3 h-3 rounded-full bg-[#F4BF4F]"></div>
						<div class="w-3 h-3 rounded-full bg-[#61C554]"></div>
					</div>
					<!-- Sidebar Icon -->
					<div
						class="text-neutral-500/80 hover:text-neutral-700 transition-colors ml-2 cursor-pointer"
					>
						<svg
							class="w-4 h-4"
							viewBox="0 0 24 24"
							fill="none"
							stroke="currentColor"
							stroke-width="2"
							stroke-linecap="round"
							stroke-linejoin="round"
						>
							<rect
								x="3"
								y="3"
								width="18"
								height="18"
								rx="2"
								stroke-width="1.8"
							/>
							<path d="M9 3v18" stroke-width="1.8" />
						</svg>
					</div>
					<!-- Separator -->
					<div class="h-3 w-[1px] bg-neutral-300/80"></div>
					<!-- Chevron Down -->
					<div
						class="text-neutral-500/80 hover:text-neutral-700 transition-colors cursor-pointer"
					>
						<svg
							class="w-3.5 h-3.5"
							viewBox="0 0 24 24"
							fill="none"
							stroke="currentColor"
							stroke-width="2.5"
							stroke-linecap="round"
							stroke-linejoin="round"
						>
							<path d="m6 9 6 6 6-6" />
						</svg>
					</div>
					<!-- Chevron Left (Disabled/Grayed) -->
					<div class="text-neutral-300 pl-0.5">
						<svg
							class="w-4 h-4"
							viewBox="0 0 24 24"
							fill="none"
							stroke="currentColor"
							stroke-width="2.2"
							stroke-linecap="round"
							stroke-linejoin="round"
						>
							<path d="m15 18-6-6 6-6" />
						</svg>
					</div>
				</div>

				<!-- Address Bar -->
				<div
					class="flex-grow ml-3 mr-1.5 h-8 rounded-md bg-cream-200/50 border border-cream-300/30 px-2.5 flex items-center justify-between"
				>
					<!-- Domain info with icon & lock -->
					<div class="flex items-center space-x-2 pl-0.5">
						<img
							src="/assets/img/favicon.png"
							alt="Favicon"
							class="w-[15px] h-[15px] rounded-[3px] select-none"
						/>
						<span
							class="text-neutral-700 text-[11px] font-medium tracking-tight"
							>getmaterio.app</span
						>
						<svg
							class="w-2.5 h-2.5 text-neutral-400/90"
							viewBox="0 0 24 24"
							fill="currentColor"
						>
							<rect
								x="3"
								y="11"
								width="18"
								height="11"
								rx="2"
								ry="2"
							/>
							<path
								d="M7 11V7a5 5 0 0 1 10 0v4"
								fill="none"
								stroke="currentColor"
								stroke-width="2"
								stroke-linecap="round"
								stroke-linejoin="round"
							/>
						</svg>
					</div>
					<!-- Horizontal Menu Dots inside translucent circle -->
					<div
						class="w-5 h-5 rounded-full bg-neutral-500/10 flex items-center justify-center text-neutral-500 hover:bg-neutral-500/20 transition-colors cursor-pointer"
					>
						<svg
							class="w-3 h-3"
							viewBox="0 0 24 24"
							fill="currentColor"
						>
							<circle cx="12" cy="12" r="1.5" />
							<circle cx="6" cy="12" r="1.5" />
							<circle cx="18" cy="12" r="1.5" />
						</svg>
					</div>
				</div>

				<!-- Extra Actions -->
				<div
					class="flex items-center justify-end text-neutral-500/80 pl-2"
				>
					<div
						class="hover:text-neutral-700 transition-colors cursor-pointer p-0.5"
					>
						<svg
							class="w-4.5 h-4.5"
							viewBox="0 0 24 24"
							fill="none"
							stroke="currentColor"
							stroke-width="2.2"
							stroke-linecap="round"
							stroke-linejoin="round"
						>
							<path d="M5 12h14" />
							<path d="M12 5v14" />
						</svg>
					</div>
				</div>
			</div>

			<div class="flex-grow bg-cream-50/10 relative">
				<img
					src="/assets/img/hero.webp"
					alt="Materio Workspace"
					class="w-full h-full object-cover object-top"
				/>
			</div>
		</div>
	</div>

	<section
		id="problem"
		class="w-full max-w-5xl flex flex-col pt-12 border-t border-cream-200 mb-24 space-y-8"
	>
		<!-- Heading and Paragraph Row -->
		<div class="grid grid-cols-1 md:grid-cols-12 gap-8 items-start">
			<div class="md:col-span-5 flex flex-col">
				<h2
					class="font-sans text-2xl md:text-3xl font-semibold tracking-tight text-cream-dark leading-[1.2]"
				>
					Everything you learn ends up somewhere else.
				</h2>
			</div>
			<div class="md:col-span-7 flex flex-col">
				<p
					class="font-sans text-lg md:text-xl font-normal leading-relaxed text-neutral-500"
				>
					Every semester starts the same way: notes arrive in WhatsApp
					groups, assignments appear on college portals, PDFs get
					downloaded, and useful links disappear into bookmarks.
					Materio turns that scattered collection into one curated
					learning library.
				</p>
			</div>
		</div>

		<!-- Diagram Row: Spans full width -->
		<div class="w-full">
			<!-- Visual Flow Container -->
			<div
				class="w-full bg-cream-100/25 border border-cream-300 rounded-3xl p-6 md:p-8 flex flex-col overflow-hidden relative select-none"
			>
				<!-- Dotted background pattern -->
				<div
					class="absolute inset-0 bg-[radial-gradient(#d3c9ba_1.5px,transparent_1.5px)] [background-size:24px_24px] opacity-50 pointer-events-none"
				></div>

				<!-- Desktop Layout (Left-to-Right Sankey Ecosystem Flow) -->
				<div class="relative z-10 w-full hidden md:block h-[400px]">
					<!-- Left Section: Scattered Learning Sources (occupies left 26% of container) -->
					<div
						class="absolute left-0 top-0 h-full w-[26%] flex flex-col justify-between py-2 items-end pr-4"
					>
						<!-- WhatsApp Groups -->
						<div
							class="flex items-center space-x-2 bg-cream-50 hover:bg-cream-100/60 border border-cream-300/80 pl-3.5 pr-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-1.5 translate-y-0.5 hover:-translate-y-0.5 w-fit select-none"
						>
							<HugeiconsIcon
								icon={WhatsappIcon}
								size={14}
								class="text-neutral-400 flex-shrink-0"
							/>
							<span class="text-[11px] font-medium tracking-tight"
								>WhatsApp Groups</span
							>
						</div>

						<!-- College Portal -->
						<div
							class="flex items-center space-x-2 bg-cream-50 hover:bg-cream-100/60 border border-cream-300/80 pl-3.5 pr-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all -rotate-2 translate-y-1 hover:-translate-y-0.5 w-fit select-none translate-x-3"
						>
							<HugeiconsIcon
								icon={UniversityIcon}
								size={14}
								class="text-neutral-400 flex-shrink-0"
							/>
							<span class="text-[11px] font-medium tracking-tight"
								>College Portal</span
							>
						</div>

						<!-- Google Drive -->
						<div
							class="flex items-center space-x-2 bg-cream-50 hover:bg-cream-100/60 border border-cream-300/80 pl-3.5 pr-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-1 -translate-y-0.5 hover:-translate-y-1 w-fit select-none -translate-x-2"
						>
							<HugeiconsIcon
								icon={GoogleDriveIcon}
								size={14}
								class="text-neutral-400 flex-shrink-0"
							/>
							<span class="text-[11px] font-medium tracking-tight"
								>Google Drive</span
							>
						</div>

						<!-- Downloads -->
						<div
							class="flex items-center space-x-2 bg-cream-50 hover:bg-cream-100/60 border border-cream-300/80 pl-3.5 pr-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all -rotate-1.5 translate-y-0.5 hover:-translate-y-0.5 w-fit select-none translate-x-2"
						>
							<HugeiconsIcon
								icon={Download01Icon}
								size={14}
								class="text-neutral-400 flex-shrink-0"
							/>
							<span class="text-[11px] font-medium tracking-tight"
								>Downloads</span
							>
						</div>

						<!-- Bookmarks -->
						<div
							class="flex items-center space-x-2 bg-cream-50 hover:bg-cream-100/60 border border-cream-300/80 pl-3.5 pr-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-2 -translate-y-1 hover:-translate-y-1.5 w-fit select-none -translate-x-1"
						>
							<HugeiconsIcon
								icon={Bookmark01Icon}
								size={14}
								class="text-neutral-400 flex-shrink-0"
							/>
							<span class="text-[11px] font-medium tracking-tight"
								>Bookmarks</span
							>
						</div>
					</div>

					<!-- Center Section: materio Logo focal point (occupies center x=38% to x=52%) -->
					<div
						class="absolute left-[38%] top-0 h-full w-[14%] flex items-center justify-center"
					>
						<div
							class="bg-cream-50 border border-cream-300 px-5 py-4 rounded-3xl shadow-[0_4px_25px_rgba(235,94,40,0.08)] hover:shadow-[0_4px_35px_rgba(235,94,40,0.18)] hover:border-brand-400/80 transition-all w-full flex items-center justify-center select-none relative z-25"
						>
							<img
								src="/assets/img/materio_new_bk.svg"
								alt="materio"
								class="h-6 w-auto select-none"
							/>
						</div>
					</div>

					<!-- Right Section: One Curated Learning Library (occupies right 44% of container) -->
					<div
						class="absolute right-0 top-0 h-full w-[44%] flex flex-col justify-center pl-4"
					>
						<!-- Inset Rounded Box Container with subtle dotted backdrop -->
						<div
							class="w-full bg-cream-50/20 border border-cream-300 rounded-3xl p-6 flex flex-col justify-between h-[360px] relative select-none shadow-[inset_0_2px_4px_rgba(0,0,0,0.01)]"
						>
							<!-- Row 1 -->
							<div class="flex flex-wrap gap-3 items-center">
								<!-- Pill 1: Official Notes -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 pl-3 pr-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-1 translate-y-0.5 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<path
											d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"
										/>
										<polyline points="14 2 14 8 20 8" />
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Official Notes</span
									>
								</div>

								<!-- Pill 2: Question Banks -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 pl-3 pr-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all -rotate-1 -translate-y-0.5 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<circle cx="12" cy="12" r="10" />
										<path
											d="M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3"
										/>
										<line
											x1="12"
											y1="17"
											x2="12.01"
											y2="17"
										/>
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Question Banks</span
									>
								</div>
							</div>

							<!-- Row 2 -->
							<div class="flex flex-wrap gap-3 items-center">
								<!-- Pill 3: Previous Year Papers -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 pl-3.5 pr-4 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-1.5 translate-y-1 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<circle cx="12" cy="12" r="10" />
										<polyline points="12 6 12 12 16 14" />
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Previous Year Papers</span
									>
								</div>

								<!-- Pill 4: Reference Books -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 px-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all -rotate-1 translate-y-0.5 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<path
											d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"
										/>
										<path
											d="M4 4.5A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1-2.5-2.5v-15z"
										/>
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Reference Books</span
									>
								</div>
							</div>

							<!-- Row 3 -->
							<div class="flex flex-wrap gap-3 items-center">
								<!-- Pill 5: Lecture Slides -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 px-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-0.5 -translate-y-1 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<rect
											x="2"
											y="3"
											width="20"
											height="14"
											rx="2"
											ry="2"
										/>
										<line x1="8" y1="21" x2="16" y2="21" />
										<line x1="12" y1="17" x2="12" y2="21" />
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Lecture Slides</span
									>
								</div>

								<!-- Pill 6: Lab Manuals -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 px-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all -rotate-1.5 translate-y-1 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<path d="M6 3h12v18H6z" />
										<path d="M10 6h4M10 10h4M10 14h4" />
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Lab Manuals</span
									>
								</div>
							</div>

							<!-- Row 4 -->
							<div class="flex flex-wrap gap-3 items-center">
								<!-- Pill 7: Assignments -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 px-3.5 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-1 -translate-y-0.5 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<path
											d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7"
										/>
										<path
											d="M18.5 2.5a2.121 2.121 0 1 1 3 3L12 15l-4 1 1-4z"
										/>
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Assignments</span
									>
								</div>

								<!-- Pill 8: Practical Files -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 px-3 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all -rotate-1 translate-y-1 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<path
											d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2"
										/>
										<rect
											x="8"
											y="2"
											width="8"
											height="4"
											rx="1"
											ry="1"
										/>
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Practical Files</span
									>
								</div>

								<!-- Pill 9: Articles -->
								<div
									class="flex items-center space-x-1.5 bg-cream-50 hover:bg-cream-100/60 border border-cream-200/55 px-3 py-2.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600 transition-all rotate-2 translate-y-0.5 select-none hover:border-cream-300"
								>
									<svg
										class="w-3.5 h-3.5 text-neutral-400 flex-shrink-0"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.8"
										stroke-linecap="round"
										stroke-linejoin="round"
									>
										<path
											d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"
										/>
										<path
											d="M16 8h2M16 12h2M16 16h2M6 8h6v8H6z"
										/>
									</svg>
									<span
										class="text-[11px] font-medium tracking-tight"
										>Articles</span
									>
								</div>
							</div>
						</div>
					</div>

					<!-- SVG Curved Sankey Connectors (Responsive overlay based on percentage values) -->
					<svg
						class="absolute inset-0 w-full h-full text-cream-400 pointer-events-none"
						viewBox="0 0 100 100"
						fill="none"
						preserveAspectRatio="none"
					>
						<!-- Left elements to center logo (lines converge to left edge of logo x=38, y=50) -->
						<path
							d="M 26 10 C 32 10, 32 50, 38 50"
							stroke="#b0a494"
							stroke-width="0.18"
							stroke-linecap="round"
						/>
						<path
							d="M 26 30 C 32 30, 32 50, 38 50"
							stroke="#b0a494"
							stroke-width="0.18"
							stroke-linecap="round"
						/>
						<path
							d="M 26 50 L 38 50"
							stroke="#b0a494"
							stroke-width="0.18"
							stroke-linecap="round"
						/>
						<path
							d="M 26 70 C 32 70, 32 50, 38 50"
							stroke="#b0a494"
							stroke-width="0.18"
							stroke-linecap="round"
						/>
						<path
							d="M 26 90 C 32 90, 32 50, 38 50"
							stroke="#b0a494"
							stroke-width="0.18"
							stroke-linecap="round"
						/>

						<!-- Neutral circle anchor nodes on left -->
						<circle cx="26" cy="10" r="0.4" fill="#b0a494" />
						<circle cx="26" cy="30" r="0.4" fill="#b0a494" />
						<circle cx="26" cy="50" r="0.4" fill="#b0a494" />
						<circle cx="26" cy="70" r="0.4" fill="#b0a494" />
						<circle cx="26" cy="90" r="0.4" fill="#b0a494" />

						<!-- One single line exits the logo right edge (52, 50) connecting to the library container left edge (56, 50) -->
						<path
							d="M 52 50 L 56 50"
							stroke="#b0a494"
							stroke-width="0.18"
							stroke-linecap="round"
						/>

						<!-- Single circle anchor node at library entrance point -->
						<circle cx="56" cy="50" r="0.4" fill="#b0a494" />
					</svg>
				</div>

				<!-- Mobile/Tablet Layout (Graceful Fallback Stack) -->
				<div
					class="relative z-10 w-full md:hidden flex flex-col items-center space-y-6"
				>
					<!-- Mobile Scattered Resources (Top) -->
					<div class="w-full flex flex-col items-center space-y-3.5">
						<span
							class="text-[9px] font-bold text-neutral-400 uppercase tracking-widest block mb-1"
						>
							Scattered Resources
						</span>
						<div class="flex flex-wrap justify-center gap-3.5">
							<div
								class="flex items-center space-x-2 bg-cream-50 border border-cream-300/80 px-3.5 py-2 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<HugeiconsIcon
									icon={WhatsappIcon}
									size={14}
									class="text-neutral-400 flex-shrink-0"
								/>
								<span class="text-xs font-medium"
									>WhatsApp Groups</span
								>
							</div>
							<div
								class="flex items-center space-x-2 bg-cream-50 border border-cream-300/80 px-3.5 py-2 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<HugeiconsIcon
									icon={UniversityIcon}
									size={14}
									class="text-neutral-400 flex-shrink-0"
								/>
								<span class="text-xs font-medium"
									>College Portal</span
								>
							</div>
							<div
								class="flex items-center space-x-2 bg-cream-50 border border-cream-300/80 px-3.5 py-2 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<HugeiconsIcon
									icon={GoogleDriveIcon}
									size={14}
									class="text-neutral-400 flex-shrink-0"
								/>
								<span class="text-xs font-medium"
									>Google Drive</span
								>
							</div>
							<div
								class="flex items-center space-x-2 bg-cream-50 border border-cream-300/80 px-3.5 py-2 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<HugeiconsIcon
									icon={Download01Icon}
									size={14}
									class="text-neutral-400 flex-shrink-0"
								/>
								<span class="text-xs font-medium"
									>Downloads</span
								>
							</div>
							<div
								class="flex items-center space-x-2 bg-cream-50 border border-cream-300/80 px-3.5 py-2 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<HugeiconsIcon
									icon={Bookmark01Icon}
									size={14}
									class="text-neutral-400 flex-shrink-0"
								/>
								<span class="text-xs font-medium"
									>Bookmarks</span
								>
							</div>
						</div>
					</div>

					<!-- Mobile Centered wordmark separator (Darkened Line) -->
					<div class="w-[1.5px] h-8 bg-[#b0a494]"></div>

					<!-- Central logo with brand glow -->
					<div
						class="bg-cream-50 border border-cream-300 px-6 py-3.5 rounded-2xl shadow-[0_4px_25px_rgba(235,94,40,0.08)] hover:shadow-[0_4px_35px_rgba(235,94,40,0.18)] hover:border-brand-400/80 transition-all"
					>
						<img
							src="/assets/img/materio_new_bk.svg"
							alt="materio"
							class="h-5 w-auto"
						/>
					</div>

					<!-- Mobile Centered wordmark separator (Darkened Line) -->
					<div class="w-[1.5px] h-8 bg-[#b0a494]"></div>

					<!-- Mobile Curated Library (Bottom) -->
					<div
						class="w-full bg-cream-50/20 border border-cream-300 rounded-3xl p-5 flex flex-col items-center space-y-4 shadow-[inset_0_2px_4px_rgba(0,0,0,0.01)]"
					>
						<span
							class="text-[9px] font-bold text-neutral-400 uppercase tracking-widest block text-center"
						>
							One Curated Learning Library
						</span>
						<div class="flex flex-wrap justify-center gap-3">
							<!-- Pill 1: Official Notes -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Official Notes</span
								>
							</div>
							<!-- Pill 2: Question Banks -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Question Banks</span
								>
							</div>
							<!-- Pill 3: Previous Year Papers -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Previous Year Papers</span
								>
							</div>
							<!-- Pill 4: Reference Books -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Reference Books</span
								>
							</div>
							<!-- Pill 5: Lecture Slides -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Lecture Slides</span
								>
							</div>
							<!-- Pill 6: Lab Manuals -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Lab Manuals</span
								>
							</div>
							<!-- Pill 7: Assignments -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Assignments</span
								>
							</div>
							<!-- Pill 8: Practical Files -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium"
									>Practical Files</span
								>
							</div>
							<!-- Pill 9: Articles -->
							<div
								class="flex items-center space-x-1.5 bg-cream-50 border border-cream-200/55 px-3 py-1.5 rounded-full shadow-[0_4px_12px_rgba(0,0,0,0.03)] text-neutral-600"
							>
								<span class="text-xs font-medium">Articles</span
								>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</section>

	<!-- Info Grid (Below the Hero Card) -->
	{#if posts.length > 0}
		<div
			class="w-full max-w-5xl grid grid-cols-1 md:grid-cols-12 gap-8 pt-6 border-t border-cream-200 mb-24"
		>
			<div class="md:col-span-4 flex flex-col">
				<a
					href="#updates"
					class="inline-flex items-center space-x-1.5 text-xs font-semibold text-brand-600 hover:text-brand-700 transition-colors uppercase tracking-wider mb-2"
				>
					<span>Latest Updates</span>
					<HugeiconsIcon icon={ArrowRight01Icon} size={14} />
				</a>
			</div>
			<div class="md:col-span-8 flex flex-col space-y-8">
				{#each posts as post}
					<div class="flex flex-col space-y-1">
						{#if post.date}
							<div
								class="text-[11px] font-semibold text-neutral-400 uppercase tracking-widest"
							>
								{formatDate(post.date)}
							</div>
						{/if}
						<p
							class="font-sans text-lg md:text-xl font-normal leading-relaxed text-neutral-500"
						>
							<span class="text-cream-dark font-semibold"
								>{post.title}{post.title.trim().endsWith(".") ||
								post.title.trim().endsWith("?") ||
								post.title.trim().endsWith("!")
									? ""
									: "."}</span
							>
							{post.excerpt}
							<a
								href="https://getmaterio.app/{post.category ||
									'whats-new'}/{post.slug}"
								target="_blank"
								class="inline-flex items-center ml-2 text-xs font-semibold text-brand-600 hover:text-brand-700 transition-colors uppercase tracking-wider group"
							>
								<span class="group-hover:underline">Read</span>
								<HugeiconsIcon
									icon={ArrowRight01Icon}
									size={11}
									class="ml-1 transition-transform group-hover:translate-x-0.5"
								/>
							</a>
						</p>
					</div>
				{/each}
			</div>
		</div>
	{/if}

	<!-- Features Section (Premium Editorial Zig-Zag Layout) -->
	<section
		id="features"
		class="w-full max-w-5xl mb-32 flex flex-col space-y-24 md:space-y-36"
	>
		<!-- Feature 1: NAVIGATION -->
		<div
			class="grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-16 items-center"
		>
			<!-- Text Column (Left) -->
			<div class="md:col-span-5 flex flex-col space-y-4">
				<span
					class="text-[10px] font-bold text-neutral-400 uppercase tracking-widest block"
				>
					01 / Navigation
				</span>
				<h3
					class="font-sans text-2xl md:text-3xl font-semibold tracking-tight text-cream-dark leading-tight"
				>
					Everything starts here.
				</h3>
				<p class="font-sans text-base leading-relaxed text-neutral-500">
					Finding the right material shouldn't require digging through
					folders. Materio's navigation is designed around how
					students actually study, making every subject, topic and
					resource easy to reach.
				</p>
				<span
					class="text-xs text-neutral-400 font-semibold tracking-tight pt-2 border-t border-cream-200/60 w-fit"
				>
					Quick access to semesters, subjects, and recent files.
				</span>
			</div>

			<!-- Preview Container Column (Right) -->
			<div class="md:col-span-7">
				<!-- svelte-ignore a11y_no_static_element_interactions -->
				<div
					class="w-full bg-cream-100/25 border-[3px] border-cream-300 rounded-3xl relative select-none shadow-[0_8px_30px_rgba(0,0,0,0.015)] h-[300px] md:h-[380px] flex items-center justify-center overflow-hidden group"
					oncontextmenu={(e) => e.preventDefault()}
				>
					<!-- Blurred WebP background card -->
					<div
						class="absolute inset-0 transition-colors overflow-hidden"
					>
						<!-- WebP Image -->
						<div
							class="absolute inset-0 bg-cover bg-center"
							style="background-image: url('/assets/img/52ceb5a9-f834-4a67-a09b-61143afbd2bc.webp');"
						></div>
						<!-- Light Blur Overlay -->
						<div
							class="absolute inset-0 bg-white/40 backdrop-blur-[13px]"
						></div>
					</div>

					<!-- Foreground Screenshot Image (positioned top-20%, scaled up to naturally clip the bottom edge) -->
					<img
						src="/assets/img/22sd1df60p.webp"
						alt="Navigation preview"
						class="absolute top-[20%] right-0 w-[80%] md:w-[70%] h-auto object-contain z-20"
					/>
				</div>
			</div>
		</div>

		<!-- Feature 2: THINKLET -->
		<div
			class="grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-16 items-center"
		>
			<!-- Preview Container Column (Left) - Order 2 on Mobile, Order 1 on Desktop -->
			<div class="md:col-span-7 order-2 md:order-1">
				<!-- svelte-ignore a11y_no_static_element_interactions -->
				<div
					class="w-full bg-cream-100/25 border-[3px] border-cream-300 rounded-3xl relative select-none shadow-[0_8px_30px_rgba(0,0,0,0.015)] h-[300px] md:h-[380px] flex items-center justify-center overflow-hidden"
					oncontextmenu={(e) => e.preventDefault()}
				>
					<!-- Blurred WebP background card -->
					<div
						class="absolute inset-0 transition-colors overflow-hidden"
					>
						<!-- WebP Image -->
						<div
							class="absolute inset-0 bg-cover bg-center"
							style="background-image: url('/assets/img/db78586b-1e3c-4e9e-8856-e57bc9a393ad.webp');"
						></div>
						<!-- Light Blur Overlay -->
						<div
							class="absolute inset-0 bg-white/40 backdrop-blur-[13px]"
						></div>
					</div>

					<!-- Centered Foreground Screenshot Image (zoomed to reduce outer margins) -->
					<img
						src="/assets/img/da3de6dd-945a-4faf-899b-7a1a22e473ed.webp"
						alt="Thinklet preview"
						class="w-[95%] md:w-[92%] h-auto max-h-[92%] object-contain z-20"
					/>
				</div>
			</div>

			<!-- Text Column (Right) - Order 1 on Mobile, Order 2 on Desktop -->
			<div
				class="md:col-span-5 flex flex-col space-y-4 order-1 md:order-2"
			>
				<span
					class="text-[10px] font-bold text-neutral-400 uppercase tracking-widest block"
				>
					02 / Thinklet
				</span>
				<h3
					class="font-sans text-2xl md:text-3xl font-semibold tracking-tight text-cream-dark leading-tight"
				>
					Your study companion, built in.
				</h3>
				<p class="font-sans text-base leading-relaxed text-neutral-500">
					Thinklet helps you understand your materials instead of just
					searching them. Ask questions, generate summaries and
					explore complex concepts naturally without ever leaving the
					page.
				</p>
				<span
					class="text-xs text-neutral-400 font-semibold tracking-tight pt-2 border-t border-cream-200/60 w-fit"
				>
					Context-aware AI assistance with in-text citations.
				</span>
			</div>
		</div>

		<!-- Feature 3: PERSONALIZATION -->
		<div
			class="grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-16 items-center"
		>
			<!-- Text Column (Left) -->
			<div class="md:col-span-5 flex flex-col space-y-4">
				<span
					class="text-[10px] font-bold text-neutral-400 uppercase tracking-widest block"
				>
					03 / Personalization
				</span>
				<h3
					class="font-sans text-2xl md:text-3xl font-semibold tracking-tight text-cream-dark leading-tight"
				>
					Built around the way you study.
				</h3>
				<p class="font-sans text-base leading-relaxed text-neutral-500">
					Materio remembers your progress and keeps your library ready
					whenever you return, creating a seamless experience across
					every study session.
				</p>
				<span
					class="text-xs text-neutral-400 font-semibold tracking-tight pt-2 border-t border-cream-200/60 w-fit"
				>
					Continue reading indicators and personalized study
					recommendations.
				</span>
			</div>

			<!-- Preview Container Column (Right) -->
			<div class="md:col-span-7">
				<!-- svelte-ignore a11y_no_static_element_interactions -->
				<div
					class="w-full bg-cream-100/25 border-[3px] border-cream-300 rounded-3xl relative select-none shadow-[0_8px_30px_rgba(0,0,0,0.015)] h-[300px] md:h-[380px] flex items-center justify-center overflow-hidden"
					oncontextmenu={(e) => e.preventDefault()}
				>
					<!-- Blurred WebP background card -->
					<div
						class="absolute inset-0 transition-colors overflow-hidden"
					>
						<!-- WebP Image -->
						<div
							class="absolute inset-0 bg-cover bg-center"
							style="background-image: url('/assets/img/5481d080-ec1d-4f4a-9feb-b477e3ea4bb6.webp');"
						></div>
						<!-- Light Blur Overlay -->
						<div
							class="absolute inset-0 bg-white/40 backdrop-blur-[13px]"
						></div>
					</div>

					<!-- Foreground Screenshot Image (positioned top-20%, scaled up to naturally clip the bottom edge) -->
					<img
						src="/assets/img/Frame@2160p.webp"
						alt="Personalization preview"
						class="absolute top-[20%] right-0 w-[80%] md:w-[70%] h-auto object-contain z-20"
					/>
				</div>
			</div>
		</div>

		<!-- Feature 4: READING EXPERIENCE -->
		<div
			class="grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-16 items-center"
		>
			<!-- Preview Container Column (Left) - Order 2 on Mobile, Order 1 on Desktop -->
			<div class="md:col-span-7 order-2 md:order-1">
				<!-- svelte-ignore a11y_no_static_element_interactions -->
				<div
					class="w-full bg-cream-100/25 border-[3px] border-cream-300 rounded-3xl relative select-none shadow-[0_8px_30px_rgba(0,0,0,0.015)] h-[300px] md:h-[380px] flex items-center justify-center overflow-hidden"
					oncontextmenu={(e) => e.preventDefault()}
				>
					<!-- Blurred WebP background card -->
					<div
						class="absolute inset-0 transition-colors overflow-hidden"
					>
						<!-- WebP Image -->
						<div
							class="absolute inset-0 bg-cover bg-center"
							style="background-image: url('/assets/img/7bf7b2ae-9959-4411-9bdf-56a144f60428.webp');"
						></div>
						<!-- Light Blur Overlay -->
						<div
							class="absolute inset-0 bg-white/40 backdrop-blur-[13px]"
						></div>
					</div>

					<!-- Foreground Screenshot Image (horizontally centered, aligned to top-[25px], scaled down slightly to naturally clip at the bottom) -->
					<img
						src="/assets/img/5rm2160p.webp"
						alt="Reading Experience preview"
						class="absolute top-[25px] left-1/2 -translate-x-1/2 w-[85%] md:w-[80%] h-auto object-contain z-20"
					/>
				</div>
			</div>

			<!-- Text Column (Right) - Order 1 on Mobile, Order 2 on Desktop -->
			<div
				class="md:col-span-5 flex flex-col space-y-4 order-1 md:order-2"
			>
				<span
					class="text-[10px] font-bold text-neutral-400 uppercase tracking-widest block"
				>
					04 / Reading Modes
				</span>
				<h3
					class="font-sans text-2xl md:text-3xl font-semibold tracking-tight text-cream-dark leading-tight"
				>
					Comfortable for every study session.
				</h3>
				<p class="font-sans text-base leading-relaxed text-neutral-500">
					Choose the reading experience that fits your environment.
					Whether you're reading during the day, at night or on an
					e-ink display, Materio adapts with you.
				</p>
				<span
					class="text-xs text-neutral-400 font-semibold tracking-tight pt-2 border-t border-cream-200/60 w-fit"
				>
					Support for Paper, Night, Inversion, and E-Ink modes.
				</span>
			</div>
		</div>

		<!-- Feature 5: INSIGHTROOM -->
		<div
			class="grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-16 items-center"
		>
			<!-- Text Column (Left) -->
			<div class="md:col-span-5 flex flex-col space-y-4">
				<span
					class="text-[10px] font-bold text-neutral-400 uppercase tracking-widest block"
				>
					05 / InsightRoom
				</span>
				<h3
					class="font-sans text-2xl md:text-3xl font-semibold tracking-tight text-cream-dark leading-tight"
				>
					Go beyond the syllabus.
				</h3>
				<p class="font-sans text-base leading-relaxed text-neutral-500">
					Discover original articles, educational explainers and
					AI-powered summaries that help you understand topics more
					deeply.
				</p>
				<span
					class="text-xs text-neutral-400 font-semibold tracking-tight pt-2 border-t border-cream-200/60 w-fit"
				>
					Curated insights and visual explainers for complex subjects.
				</span>
			</div>

			<!-- Preview Container Column (Right) -->
			<div class="md:col-span-7">
				<!-- svelte-ignore a11y_no_static_element_interactions -->
				<div
					class="w-full bg-cream-100/25 border-[3px] border-cream-300 rounded-3xl relative select-none shadow-[0_8px_30px_rgba(0,0,0,0.015)] h-[300px] md:h-[380px] flex items-center justify-center overflow-hidden"
					oncontextmenu={(e) => e.preventDefault()}
				>
					<!-- Blurred WebP background card -->
					<div
						class="absolute inset-0 transition-colors overflow-hidden"
					>
						<!-- WebP Image -->
						<div
							class="absolute inset-0 bg-cover bg-center"
							style="background-image: url('/assets/img/9738efed-f1d0-40ba-9b5d-e865413081c4.webp');"
						></div>
						<!-- Light Blur Overlay -->
						<div
							class="absolute inset-0 bg-white/40 backdrop-blur-[13px]"
						></div>
					</div>

					<!-- Foreground Screenshot Image (scaled up even more and top-aligned to push it down and clip the bottom edge) -->
					<img
						src="/assets/img/mr34eyy450p.webp"
						alt="Flashcards preview"
						class="absolute top-[25%] right-0 w-[105%] md:w-[95%] h-auto object-contain z-20"
					/>
				</div>
			</div>
		</div>

		<!-- Feature 6: SEARCH -->
		<div
			class="grid grid-cols-1 md:grid-cols-12 gap-8 md:gap-16 items-center"
		>
			<!-- Preview Container Column (Left) - Order 2 on Mobile, Order 1 on Desktop -->
			<div class="md:col-span-7 order-2 md:order-1">
				<!-- svelte-ignore a11y_no_static_element_interactions -->
				<div
					class="w-full bg-cream-100/25 border-[3px] border-cream-300 rounded-3xl relative select-none shadow-[0_8px_30px_rgba(0,0,0,0.015)] h-[300px] md:h-[380px] flex items-center justify-center overflow-hidden"
					oncontextmenu={(e) => e.preventDefault()}
				>
					<!-- Blurred WebP background card -->
					<div
						class="absolute inset-0 transition-colors overflow-hidden"
					>
						<!-- WebP Image -->
						<div
							class="absolute inset-0 bg-cover bg-center"
							style="background-image: url('/assets/img/24d4ff6ts.webp');"
						></div>
						<!-- Light Blur Overlay -->
						<div
							class="absolute inset-0 bg-white/40 backdrop-blur-[13px]"
						></div>
					</div>

					<!-- Foreground Screenshot Image (middle-aligned, bottom-clipped) -->
					<img
						src="/assets/img/ae28fdl60p.webp"
						alt="Search preview"
						class="absolute bottom-[-20px] left-1/2 -translate-x-1/2 h-[92%] w-auto object-contain z-20"
					/>
				</div>
			</div>

			<!-- Text Column (Right) - Order 1 on Mobile, Order 2 on Desktop -->
			<div
				class="md:col-span-5 flex flex-col space-y-4 order-1 md:order-2"
			>
				<span
					class="text-[10px] font-bold text-neutral-400 uppercase tracking-widest block"
				>
					06 / Search
				</span>
				<h3
					class="font-sans text-2xl md:text-3xl font-semibold tracking-tight text-cream-dark leading-tight"
				>
					Find exactly what you're looking for.
				</h3>
				<p class="font-sans text-base leading-relaxed text-neutral-500">
					Search across your entire library in seconds. Whether it's a
					chapter, concept, question bank or lecture slides, Materio
					surfaces the most relevant materials from your curated
					collection.
				</p>
				<span
					class="text-xs text-neutral-400 font-semibold tracking-tight pt-2 border-t border-cream-200/60 w-fit"
				>
					Fast semantic search across verified study materials.
				</span>
			</div>
		</div>
	</section>

	<!-- Connectors Section (3D Tilted Grid & Fallback State) -->
	<section
		id="connectors"
		class="w-full max-w-5xl mb-32 flex flex-col items-center text-center space-y-4"
	>
		<!-- Heading and Badge -->
		<div class="flex flex-col items-center space-y-3.5 max-w-2xl">
			<span
				class="inline-flex items-center space-x-1 text-xs font-semibold text-brand-600 hover:text-brand-700 transition-colors"
			>
				Connectors
			</span>
			<h2
				class="font-sans text-3xl md:text-4xl font-semibold tracking-tight text-cream-dark leading-[1.15]"
			>
				Access the entire Materio library<br class="hidden sm:inline" />
				from your favorite AI.
			</h2>
		</div>

		<!-- 3D Perspective Grid of 6 Logos (1 Row) -->
		<div
			class="relative w-full py-0 flex flex-col items-center justify-center"
		>
			<!-- 3D Perspective Wrapper (no overflow-hidden here, so icons can elevate freely) -->
			<div
				class="perspective-container relative w-full max-w-2xl px-2 py-2 flex justify-center z-10"
			>
				<!-- Tilted Grid (1 row, 6 columns) -->
				<div
					class="grid grid-cols-6 gap-3 sm:gap-6 md:gap-8 w-full px-2 tilted-grid-transform justify-center justify-items-center"
				>
					{#each connectorLogos as logo}
						<div
							class="logo-card group relative flex items-center justify-center transition-all duration-300"
							style="aspect-ratio: 1 / 1;"
						>
							{#if !imageErrors[logo.name]}
								<img
									src={logo.img}
									alt={logo.name}
									class="w-full h-full object-contain"
									onerror={() =>
										(imageErrors[logo.name] = true)}
								/>
							{:else}
								<!-- Premium typography-based placeholder fallback -->
								<div
									class="w-full h-full flex flex-col items-center justify-center bg-white border border-cream-200/50"
									style="color: {logo.logoColor}; border-radius: 22.5%;"
								>
									<div
										class="w-8 h-8 sm:w-10 sm:h-10 rounded-xl flex items-center justify-center text-xs sm:text-sm font-black tracking-tight mb-1"
										style="background-color: {logo.logoColor}15; color: {logo.logoColor};"
									>
										{logo.name.slice(0, 2).toUpperCase()}
									</div>
									<span
										class="text-[8px] sm:text-[10px] font-bold tracking-tight text-neutral-500"
										>{logo.name}</span
									>
								</div>
							{/if}
						</div>
					{/each}
				</div>
			</div>

			<!-- 2D Overlay Container centered on screen, wrapping the grid width precisely -->
			<!-- This forces the blurs/fades to render in 2D space on top of the 3D grid and overlap the side icons correctly -->
			<div
				class="absolute inset-y-0 left-1/2 -translate-x-1/2 w-full max-w-2xl px-2 pointer-events-none z-30"
			>
				<!-- Left side progressive blur & fade -->
				<ProgressiveBlur
					position="left"
					width="auto"
					class="absolute left-0 top-0 bottom-0 z-30 w-12 md:w-[72px]"
				/>
				<div
					class="absolute left-0 top-0 bottom-0 w-8 bg-gradient-to-r from-cream-50 to-transparent z-40"
				></div>

				<!-- Right side progressive blur & fade -->
				<ProgressiveBlur
					position="right"
					width="auto"
					class="absolute right-0 top-0 bottom-0 z-30 w-12 md:w-[72px]"
				/>
				<div
					class="absolute right-0 top-0 bottom-0 w-8 bg-gradient-to-l from-cream-50 to-transparent z-40"
				></div>
			</div>
		</div>

		<!-- Bottom Description -->
		<p
			class="font-sans text-[17px] md:text-lg font-normal leading-relaxed text-neutral-500 max-w-3xl px-4"
		>
			Access Materio's curated library directly from the AI assistants and
			workspaces you already use through the Model Context Protocol (MCP).
			No switching tabs or manually copying context—just ask naturally
			from the tools you already use.
		</p>
	</section>
</main>

<footer
	class="w-full pt-16 pb-0 px-6 md:px-12 border-t border-cream-200/60 max-w-5xl mx-auto flex flex-col relative overflow-hidden"
>
	<!-- Footer Grid -->
	<div class="grid grid-cols-2 md:grid-cols-4 gap-8 md:gap-12 mb-12">
		<!-- Column 1: Product -->
		<div class="flex flex-col space-y-3.5">
			<h4
				class="text-[14px] font-semibold text-neutral-400 tracking-wide mb-2"
			>
				Product
			</h4>
			<a
				href="https://getmaterio.app"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Materio</a
			>
			<a
				href="https://room.getmaterio.app"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Insightroom</a
			>
			<a
				href="https://chat.getmaterio.app"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Thinklet</a
			>
			<a
				href="https://getmaterio.app/changelog"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Changelog</a
			>
			<a
				href="#mcp"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Materio MCP</a
			>
			<a
				href="#mcp"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>NCERT MCP</a
			>
			<a
				href="#pricing"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Pricing</a
			>
		</div>

		<!-- Column 2: Resources -->
		<div class="flex flex-col space-y-3.5">
			<h4
				class="text-[14px] font-semibold text-neutral-400 tracking-wide mb-2"
			>
				Resources
			</h4>
			<a
				href="#docs"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Docs</a
			>
			<a
				href="#faqs"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>FAQs</a
			>
		</div>

		<!-- Column 3: Company -->
		<div class="flex flex-col space-y-3.5">
			<h4
				class="text-[14px] font-semibold text-neutral-400 tracking-wide mb-2"
			>
				Company
			</h4>
			<a
				href="https://getmaterio.app/whatisthis"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>About</a
			>
			<a
				href="https://github.com/Materioa"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Github</a
			>
			<a
				href="mailto:hello@getmaterio.app"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Contact</a
			>
		</div>

		<!-- Column 4: Legal -->
		<div class="flex flex-col space-y-3.5">
			<h4
				class="text-[14px] font-semibold text-neutral-400 tracking-wide mb-2"
			>
				Legal
			</h4>
			<a
				href="https://getmaterio.app/privacy"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Privacy</a
			>
			<a
				href="https://getmaterio.app/terms"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Terms</a
			>
			<a
				href="https://getmaterio.app/cookies"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>Cookies</a
			>
			<a
				href="https://getmaterio.app/license"
				target="_blank"
				class="text-[16px] font-semibold text-cream-dark hover:opacity-75 transition-opacity"
				>License</a
			>
		</div>
	</div>

	<!-- Bottom Row -->
	<div
		class="flex flex-col sm:flex-row items-center justify-between py-6 border-t border-cream-200/40 text-neutral-400 text-xs gap-4 sm:gap-0 z-10"
	>
		<div>
			© 2026 <span class="mx-1 text-neutral-300">|</span> Crafted with
			<span class="heart-red inline-block align-middle pb-0.5"
				><HugeiconsIcon icon={HeartIcon} size={13} /></span
			> by Jinansh
		</div>
		<div class="flex items-center space-x-2">
			<!-- Status indicator dot -->
			<span class="relative flex h-2 w-2">
				{#if statusType === "operational"}
					<span
						class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"
					></span>
					<span
						class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500"
					></span>
				{:else if statusType === "checking"}
					<span
						class="animate-ping absolute inline-flex h-full w-full rounded-full bg-amber-400 opacity-75"
					></span>
					<span
						class="relative inline-flex rounded-full h-2 w-2 bg-amber-500"
					></span>
				{:else}
					<span
						class="animate-ping absolute inline-flex h-full w-full rounded-full bg-rose-400 opacity-75"
					></span>
					<span
						class="relative inline-flex rounded-full h-2 w-2 bg-rose-500"
					></span>
				{/if}
			</span>
			<span
				class="font-semibold text-[13px] {statusType === 'operational'
					? 'text-emerald-700/90'
					: statusType === 'checking'
						? 'text-amber-700/90'
						: 'text-rose-700/90'}">{statusText}</span
			>
		</div>
	</div>

	<!-- Giant Faded Logo Background -->
	<div
		class="w-full flex justify-center pointer-events-none select-none overflow-hidden mt-6 mb-[-36px] md:mb-[-72px]"
		style="mask-image: linear-gradient(to bottom, oklch(0 0 0 / 0.28) 0%, oklch(0 0 0 / 0) 100%); -webkit-mask-image: linear-gradient(to bottom, oklch(0 0 0 / 0.28) 0%, oklch(0 0 0 / 0) 100%);"
	>
		<img
			src="/assets/img/materio_new_bk.svg"
			alt="materio watermark"
			class="w-[500px] sm:w-[700px] md:w-[900px] max-w-none h-auto"
		/>
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
			<a
				href="/"
				class="flex items-center ml-1"
				onclick={() => (mobileMenuOpen = false)}
			>
				<img
					src="/assets/img/materio_new_bk.svg"
					alt="materio"
					class="h-7 w-auto"
				/>
			</a>
			<!-- Close Button -->
			<button
				onclick={() => (mobileMenuOpen = false)}
				class="p-2 -mr-2 text-neutral-800 transition-opacity hover:opacity-70"
			>
				<HugeiconsIcon icon={Cancel01Icon} size={24} />
			</button>
		</div>

		<!-- Links -->
		<div class="flex flex-col mt-4">
			<a
				href="#features"
				onclick={() => (mobileMenuOpen = false)}
				in:blurSlide={{ delay: 100 }}
				out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Features
			</a>
			<a
				href="#connectors"
				onclick={() => (mobileMenuOpen = false)}
				in:blurSlide={{ delay: 150 }}
				out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Connectors
			</a>
			<a
				href="#resources"
				onclick={() => (mobileMenuOpen = false)}
				in:blurSlide={{ delay: 200 }}
				out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Resources
			</a>
			<a
				href="#pricing"
				onclick={() => (mobileMenuOpen = false)}
				in:blurSlide={{ delay: 250 }}
				out:fade={{ duration: 150 }}
				class="py-6 px-7 border-t border-cream-200/50 font-sans text-[22px] tracking-tight text-neutral-900 hover:bg-cream-100/50 transition-colors"
			>
				Pricing
			</a>
			<a
				href="https://getmaterio.app"
				target="_blank"
				onclick={() => (mobileMenuOpen = false)}
				in:blurSlide={{ delay: 300 }}
				out:fade={{ duration: 150 }}
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
	:global(.heart-red svg),
	:global(.heart-red path) {
		fill: #ef4444 !important;
		color: #ef4444 !important;
		stroke: #ef4444 !important;
	}

	.perspective-container {
		perspective: 1200px;
		perspective-origin: 50% 0%;
	}

	.tilted-grid-transform {
		transform: rotateX(32deg) rotateY(0deg) rotateZ(0deg) scale(0.95);
		transform-style: preserve-3d;
	}

	.logo-card {
		transform-style: preserve-3d;
		transform: translateY(0) translateZ(0);
		transition:
			transform 0.45s cubic-bezier(0.34, 1.56, 0.64, 1),
			filter 0.45s cubic-bezier(0.34, 1.56, 0.64, 1);
		width: 100%;
		max-width: 76px;
		aspect-ratio: 1 / 1;
		filter: drop-shadow(0 6px 12px rgba(0, 0, 0, 0.12));
	}

	@media (min-width: 640px) {
		.logo-card {
			max-width: 92px;
		}
	}

	.logo-card:hover {
		transform: translateY(-6px) translateZ(4px);
		filter: drop-shadow(0 10px 14px rgba(0, 0, 0, 0.16));
	}
</style>
