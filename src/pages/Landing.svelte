<script lang="ts">
	import { navigate } from "svelte-routing";
	import { onMount } from "svelte";
	import { supabase } from "../lib/supabase";

	let isLoggedIn = false;
	onMount(async () => {
		const { data: { session } } = await supabase.auth.getSession();
		isLoggedIn = !!session;
	});

	const handleGetStarted = () => {
		if (isLoggedIn) {
			navigate("/dashboard");
		} else {
			navigate("/auth");
		}
	};
</script>

<div class="min-h-screen bg-slate-50 flex flex-col font-sans">
	<header class="bg-white/80 backdrop-blur-md border-b border-slate-200 sticky top-0 z-50">
		<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
			<div class="flex items-center space-x-2">
				<div class="h-9 w-9 sm:h-10 sm:w-10 rounded-xl bg-linear-to-tr from-blue-600 to-indigo-600 flex items-center justify-center shadow-md shadow-blue-200">
					<svg class="w-5 h-5 text-white" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M7.5 8.25h9m-9 3H12m-9.75 1.51c0 1.6 1.123 2.994 2.707 3.227 1.129.166 2.27.293 3.423.379.35.026.67.21.865.501L12 21l2.755-4.133a1.14 1.14 0 0 1 .865-.501 48.172 48.172 0 0 0 3.423-.379c1.584-.233 2.707-1.626 2.707-3.228V6.741c0-1.602-1.123-2.995-2.707-3.228A48.394 48.394 0 0 0 12 3c-2.392 0-4.744.175-7.043.513C3.373 3.746 2.25 5.14 2.25 6.741v6.018Z"/></svg>
				</div>
				<span class="text-lg sm:text-xl font-extrabold bg-linear-to-r from-slate-900 to-slate-700 bg-clip-text text-transparent">ChatMVP</span>
			</div>

			<div class="flex items-center space-x-2 sm:space-x-4">
				{#if isLoggedIn}
					<button on:click={() => navigate("/dashboard")} class="hidden sm:block text-sm font-semibold text-slate-700 hover:text-blue-600 transition">Dashboard</button>
				{:else}
					<button on:click={() => navigate("/auth")} class="hidden sm:block text-sm font-semibold text-slate-600 hover:text-slate-900 transition">Masuk</button>
				{/if}
				<button on:click={handleGetStarted} class="px-4 py-2 sm:px-5 sm:py-2.5 rounded-xl bg-blue-600 text-white font-semibold text-sm hover:bg-blue-700 transition shadow-lg shadow-blue-100 flex items-center space-x-1">
					<span>{isLoggedIn ? 'Buka App' : 'Mulai'}</span>
					<svg class="w-4 h-4 hidden sm:block" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5 21 12m0 0-7.5 7.5M21 12H3"/></svg>
				</button>
			</div>
		</div>
	</header>

	<section class="relative pt-10 pb-16 md:pt-20 md:pb-28 overflow-hidden">
		<div class="absolute top-0 left-1/2 -translate-x-1/2 w-full max-w-7xl h-96 opacity-30 pointer-events-none">
			<div class="absolute top-12 left-1/4 w-72 h-72 rounded-full bg-blue-400 blur-3xl"></div>
			<div class="absolute top-24 right-1/4 w-96 h-96 rounded-full bg-indigo-300 blur-3xl"></div>
		</div>

		<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 relative">
			<div class="grid grid-cols-1 lg:grid-cols-12 gap-10 lg:gap-12 items-center">
				<div class="lg:col-span-7 text-center lg:text-left space-y-6">
					<div class="inline-flex items-center space-x-2 px-3.5 py-1.5 rounded-full bg-blue-50 text-blue-700 border border-blue-100 text-[10px] sm:text-xs font-semibold uppercase tracking-wider animate-pulse">
						✨ UAS Komputasi Paralel Dan Terdistribusi Juli 2026
					</div>
					<h1 class="text-3xl sm:text-5xl lg:text-6xl font-black text-slate-900 tracking-tight leading-tight">
						Komunikasi Real-Time <br class="hidden sm:block" />
						<span class="bg-linear-to-r from-blue-600 to-indigo-600 bg-clip-text text-transparent">Lebih Aman & Cepat</span>
					</h1>
					<p class="text-sm sm:text-lg text-slate-600 max-w-2xl mx-auto lg:mx-0 leading-relaxed">
						Sistem chat real-time multi-platform yang dibangun dengan Java (WebSocket) & Svelte. Dilengkapi dengan bad-word filter cerdas, log sistem berbasis file, pesan privat, dan grup obrolan.
					</p>
					<div class="flex flex-col sm:flex-row justify-center lg:justify-start gap-3 sm:gap-4 pt-2">
						<button on:click={handleGetStarted} class="px-6 py-3.5 sm:px-8 sm:py-4 rounded-xl bg-linear-to-r from-blue-600 to-indigo-600 text-white font-bold text-sm sm:text-base hover:from-blue-700 hover:to-indigo-700 transition shadow-xl shadow-blue-200/50 flex items-center justify-center space-x-2">
							<span>Mulai Chatting Gratis</span>
							<svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12.75 15l3-3m0 0l-3-3m3 3h-7.5M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
						</button>
						<a href="#features" class="px-6 py-3.5 sm:px-8 sm:py-4 rounded-xl bg-white border border-slate-250 text-slate-700 font-semibold text-sm sm:text-base hover:bg-slate-50 hover:border-slate-300 transition flex items-center justify-center">
							Pelajari Fitur
						</a>
					</div>
				</div>

				<div class="lg:col-span-5 relative mt-6 lg:mt-0">
					<div class="relative mx-auto w-full max-w-sm sm:max-w-md lg:max-w-full">
						<div class="absolute -inset-4 rounded-3xl bg-linear-to-tr from-blue-500 to-indigo-500 opacity-20 blur-2xl"></div>
						<div class="relative bg-white rounded-2xl shadow-2xl border border-slate-200 overflow-hidden">
							<div class="bg-slate-100 px-4 py-3 border-b border-slate-200 flex items-center space-x-1.5">
								<div class="w-2.5 h-2.5 rounded-full bg-red-400"></div>
								<div class="w-2.5 h-2.5 rounded-full bg-amber-400"></div>
								<div class="w-2.5 h-2.5 rounded-full bg-green-400"></div>
								<div class="flex-1 bg-white rounded-md h-5 text-[10px] text-slate-400 flex items-center justify-center ml-4 border border-slate-200 truncate font-mono px-2">
									chatmvp.apriadisalim.com
								</div>
							</div>
							<div class="p-4 bg-slate-50 space-y-3">
								<div class="flex items-start space-x-2.5">
									<div class="w-7 h-7 rounded-full bg-slate-200 flex items-center justify-center text-[10px] font-bold text-slate-600 shrink-0">S</div>
									<div class="bg-white p-2.5 rounded-lg rounded-tl-none shadow-sm border border-slate-100 max-w-[85%]">
										<p class="text-[10px] font-bold text-blue-600">System</p>
										<p class="text-xs text-slate-600">Halo! Selamat bergabung di ChatMVP 🎉</p>
									</div>
								</div>
								<div class="flex items-start space-x-2.5 justify-end">
									<div class="bg-blue-600 p-2.5 rounded-lg rounded-tr-none shadow-sm text-white max-w-[85%]">
										<p class="text-xs">Wah, lancar sekali pesannya terkirim real-time!</p>
									</div>
									<div class="w-7 h-7 rounded-full bg-blue-100 flex items-center justify-center text-[10px] font-bold text-blue-700 shrink-0">M</div>
								</div>
								<div class="flex items-start space-x-2.5">
									<div class="w-7 h-7 rounded-full bg-amber-100 flex items-center justify-center text-[10px] font-bold text-amber-700 shrink-0">A</div>
									<div class="bg-white p-2.5 rounded-lg rounded-tl-none shadow-sm border border-slate-100 max-w-[85%]">
										<p class="text-[10px] font-bold text-amber-600">Apriadi</p>
										<p class="text-xs text-slate-600">Terima kasih, backend kami menggunakan WebSocket Javalin yang berkinerja tinggi!</p>
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</section>

	<section class="py-16 bg-slate-900 border-t border-slate-800">
		<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
			<div class="text-center mb-10">
				<h2 class="text-[10px] sm:text-xs font-bold text-blue-400 uppercase tracking-widest mb-2">Tim Pengembang</h2>
				<p class="text-2xl sm:text-3xl font-extrabold text-white">UAS Komputasi Paralel Dan Terdistribusi Juli 2026</p>
			</div>
			
			<div class="grid grid-cols-2 lg:grid-cols-4 gap-4 sm:gap-6">
				<div class="p-4 sm:p-5 rounded-2xl bg-slate-800 border border-slate-700 flex flex-col items-center text-center hover:border-blue-500/50 transition">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-full bg-blue-500/20 text-blue-400 font-bold flex items-center justify-center text-sm sm:text-lg mb-3">AS</div>
					<p class="font-bold text-white text-xs sm:text-sm">Apriadi Salim</p>
					<p class="text-[10px] sm:text-xs text-slate-400 font-mono mb-3">2410010605</p>
					<span class="px-2.5 py-1 bg-blue-500/20 text-blue-300 text-[9px] sm:text-[10px] font-bold rounded-lg border border-blue-500/30">Back End & Infra</span>
				</div>
				
				<div class="p-4 sm:p-5 rounded-2xl bg-slate-800 border border-slate-700 flex flex-col items-center text-center hover:border-indigo-500/50 transition">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-full bg-indigo-500/20 text-indigo-400 font-bold flex items-center justify-center text-sm sm:text-lg mb-3">MR</div>
					<p class="font-bold text-white text-xs sm:text-sm leading-tight mb-1">M. Reynaldy A. P.</p>
					<p class="text-[10px] sm:text-xs text-slate-400 font-mono mb-3">2410010010</p>
					<span class="px-2.5 py-1 bg-indigo-500/20 text-indigo-300 text-[9px] sm:text-[10px] font-bold rounded-lg border border-indigo-500/30">Front End</span>
				</div>

				<div class="p-4 sm:p-5 rounded-2xl bg-slate-800 border border-slate-700 flex flex-col items-center text-center hover:border-indigo-500/50 transition">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-full bg-indigo-500/20 text-indigo-400 font-bold flex items-center justify-center text-sm sm:text-lg mb-3">MH</div>
					<p class="font-bold text-white text-xs sm:text-sm">Moh Hafiz Anshari</p>
					<p class="text-[10px] sm:text-xs text-slate-400 font-mono mb-3">2410010142</p>
					<span class="px-2.5 py-1 bg-indigo-500/20 text-indigo-300 text-[9px] sm:text-[10px] font-bold rounded-lg border border-indigo-500/30">Front End</span>
				</div>
				<div class="p-4 sm:p-5 rounded-2xl bg-slate-800 border border-slate-700 flex flex-col items-center text-center hover:border-indigo-500/50 transition">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-full bg-indigo-500/20 text-indigo-400 font-bold flex items-center justify-center text-sm sm:text-lg mb-3">MR</div>
					<p class="font-bold text-white text-xs sm:text-sm">M. Reza Maulani A.</p>
					<p class="text-[10px] sm:text-xs text-slate-400 font-mono mb-3">2410010638</p>
					<span class="px-2.5 py-1 bg-indigo-500/20 text-indigo-300 text-[9px] sm:text-[10px] font-bold rounded-lg border border-indigo-500/30">Front End</span>
				</div>
			</div>
		</div>
	</section>

	<section id="features" class="bg-white py-16 sm:py-20 border-t border-slate-200">
		<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
			<div class="text-center max-w-3xl mx-auto space-y-3 mb-12 sm:mb-16">
				<h2 class="text-[10px] sm:text-xs font-bold text-blue-600 uppercase tracking-widest">Fitur Utama</h2>
				<p class="text-2xl sm:text-4xl font-extrabold text-slate-900 tracking-tight">Arsitektur Tangguh & Kaya Fitur</p>
				<p class="text-sm sm:text-lg text-slate-600">Dikembangkan khusus untuk memenuhi nilai sempurna pemrograman berbasis objek (OOP) dengan efisiensi tinggi.</p>
			</div>

			<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 sm:gap-8">
				<div class="p-5 sm:p-6 rounded-2xl bg-slate-50 border border-slate-100 hover:border-slate-200 hover:shadow-lg transition group">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-blue-100 text-blue-600 flex items-center justify-center mb-4 sm:mb-5 group-hover:scale-110 transition">
						<svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.362 5.214A8.252 8.252 0 0 1 12 21 8.25 8.25 0 0 1 6.038 7.047 8.287 8.287 0 0 0 9 9.601a8.983 8.983 0 0 1 3.361-6.867 8.21 8.21 0 0 0 3 2.48Z"/></svg>
					</div>
					<h3 class="text-base sm:text-lg font-bold text-slate-900 mb-2">WebSocket Real-Time</h3>
					<p class="text-xs sm:text-sm text-slate-600 leading-relaxed">Pesan instan terkirim dan diterima dalam hitungan milidetik tanpa polling berkala berkat WebSocket Javalin.</p>
				</div>
				<div class="p-5 sm:p-6 rounded-2xl bg-slate-50 border border-slate-100 hover:border-slate-200 hover:shadow-lg transition group">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-indigo-100 text-indigo-600 flex items-center justify-center mb-4 sm:mb-5 group-hover:scale-110 transition">
						<svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M18 18.72a9.094 9.094 0 0 0 3.741-.479 3 3 0 0 0-4.682-2.72m.94 3.198.001.031c0 .225-.012.447-.037.666A11.944 11.944 0 0 1 12 21c-2.17 0-4.207-.576-5.963-1.584A6.062 6.062 0 0 1 6 18.719m12 0a5.971 5.971 0 0 0-.941-3.197m0 0A5.995 5.995 0 0 0 12 12.75a5.995 5.995 0 0 0-5.058 2.772m0 0a3 3 0 0 0-4.681 2.72 8.986 8.986 0 0 0 3.74.477m.94-3.197a5.971 5.971 0 0 0-.94 3.197M15 6.75a3 3 0 1 1-6 0 3 3 0 0 1 6 0Zm6 3a2.25 2.25 0 1 1-4.5 0 2.25 2.25 0 0 1 4.5 0Zm-13.5 0a2.25 2.25 0 1 1-4.5 0 2.25 2.25 0 0 1 4.5 0Z"/></svg>
					</div>
					<h3 class="text-base sm:text-lg font-bold text-slate-900 mb-2">Grup & Chat Privat</h3>
					<p class="text-xs sm:text-sm text-slate-600 leading-relaxed">Ngobrol secara pribadi langsung antar-kontak atau buat grup baru dengan banyak anggota sesuai kebutuhan.</p>
				</div>
				<div class="p-5 sm:p-6 rounded-2xl bg-slate-50 border border-slate-100 hover:border-slate-200 hover:shadow-lg transition group">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-rose-100 text-rose-600 flex items-center justify-center mb-4 sm:mb-5 group-hover:scale-110 transition">
						<svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 9v3.75m0-10.036A11.959 11.959 0 0 1 3.598 6 11.99 11.99 0 0 0 3 9.75c0 5.592 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.31-.21-2.57-.598-3.75h-.152c-3.196 0-6.1-1.249-8.25-3.286Zm0 13.036h.008v.008H12v-.008Z"/></svg>
					</div>
					<h3 class="text-base sm:text-lg font-bold text-slate-900 mb-2">Sensor Kata Kasar</h3>
					<p class="text-xs sm:text-sm text-slate-600 leading-relaxed">Filter otomatis (bad-word processor) menggunakan struktur data Array untuk memblokir kata tidak sopan seketika.</p>
				</div>
				<div class="p-5 sm:p-6 rounded-2xl bg-slate-50 border border-slate-100 hover:border-slate-200 hover:shadow-lg transition group">
					<div class="w-10 h-10 sm:w-12 sm:h-12 rounded-xl bg-teal-100 text-teal-600 flex items-center justify-center mb-4 sm:mb-5 group-hover:scale-110 transition">
						<svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M19.5 14.25v-2.625a3.375 3.375 0 0 0-3.375-3.375h-1.5A1.125 1.125 0 0 1 13.5 7.125v-1.5a3.375 3.375 0 0 0-3.375-3.375H8.25m0 12.75h7.5m-7.5 3H12M10.5 2.25H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 0 0-9-9Z"/></svg>
					</div>
					<h3 class="text-base sm:text-lg font-bold text-slate-900 mb-2">Pencatatan Log</h3>
					<p class="text-xs sm:text-sm text-slate-600 leading-relaxed">Penyimpanan seluruh histori pesan secara persisten di berkas teks server untuk keperluan audit log admin.</p>
				</div>
			</div>
		</div>
	</section>

	<footer class="bg-white border-t border-slate-200 mt-auto py-6 sm:py-8">
		<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 flex flex-col md:flex-row items-center justify-between gap-3 text-center md:text-left">
			<div class="flex flex-col sm:flex-row items-center space-y-1 sm:space-y-0 sm:space-x-2">
				<span class="text-sm font-extrabold text-slate-800">ChatMVP</span>
				<span class="hidden sm:inline text-xs text-slate-400">|</span>
				<span class="text-[10px] sm:text-xs text-slate-500">Tugas Akhir Pemrograman Berorientasi Objek 1</span>
			</div>
			<div>
				<p class="text-[10px] sm:text-xs text-slate-500">
					Dibuat dengan ❤️ oleh <span class="font-bold text-slate-700">Tim UAS</span>
				</p>
			</div>
		</div>
	</footer>
</div>
