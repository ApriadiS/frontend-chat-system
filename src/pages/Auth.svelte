<script lang="ts">
	import { supabase } from "../lib/supabase";
	import { navigate } from "svelte-routing";
	import InputField from "../components/InputField.svelte";

	let email = "";
	let password = "";
	let username = "";
	let isLogin = true;

	let loading = false;
	let errorMessage = "";
	let successMessage = "";

	const handleAuth = async (e: Event) => {
		e.preventDefault();
		loading = true;
		errorMessage = "";
		successMessage = "";

		try {
			if (isLogin) {
				const { error } = await supabase.auth.signInWithPassword({ email, password });
				if (error) throw error;
				navigate("/dashboard", { replace: true });
			} else {
				const { data, error } = await supabase.auth.signUp({
					email,
					password,
					options: { data: { username: username.trim() } },
				});
				if (error) throw error;
				if (data?.session) {
					successMessage = "Pendaftaran berhasil! Mengalihkan ke dashboard...";
					setTimeout(() => navigate("/dashboard", { replace: true }), 1500);
				} else {
					successMessage = "Pendaftaran berhasil! Silakan cek email kamu untuk verifikasi.";
				}
			}
		} catch (error: any) {
			errorMessage = error.message;
		} finally {
			loading = false;
		}
	};

	const toggleMode = () => {
		isLogin = !isLogin;
		errorMessage = "";
		successMessage = "";
		username = "";
	};
</script>

<div class="min-h-screen bg-slate-50 flex flex-col justify-center py-12 sm:px-6 lg:px-8 font-sans relative overflow-hidden">
	<div class="absolute -top-40 -left-40 w-96 h-96 bg-blue-400/20 rounded-full blur-3xl pointer-events-none"></div>
	<div class="absolute -bottom-40 -right-40 w-96 h-96 bg-indigo-400/20 rounded-full blur-3xl pointer-events-none"></div>

	<div class="sm:mx-auto sm:w-full sm:max-w-md z-10">
		<button on:click={() => navigate("/")} class="mx-auto flex items-center justify-center space-x-2 focus:outline-none group">
			<div class="h-12 w-12 rounded-2xl bg-linear-to-tr from-blue-600 to-indigo-600 flex items-center justify-center shadow-lg group-hover:scale-105 transition">
				<svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M7.5 8.25h9m-9 3H12m-9.75 1.51c0 1.6 1.123 2.994 2.707 3.227 1.129.166 2.27.293 3.423.379.35.026.67.21.865.501L12 21l2.755-4.133a1.14 1.14 0 0 1 .865-.501 48.172 48.172 0 0 0 3.423-.379c1.584-.233 2.707-1.626 2.707-3.228V6.741c0-1.602-1.123-2.995-2.707-3.228A48.394 48.394 0 0 0 12 3c-2.392 0-4.744.175-7.043.513C3.373 3.746 2.25 5.14 2.25 6.741v6.018Z"/></svg>
			</div>
			<span class="text-2xl font-black bg-linear-to-r from-slate-900 to-slate-700 bg-clip-text text-transparent">ChatMVP</span>
		</button>
		<h2 class="mt-6 text-center text-3xl font-extrabold text-slate-900">{isLogin ? "Selamat Datang Kembali" : "Buat Akun ChatMVP"}</h2>
		<p class="mt-2 text-center text-sm text-slate-600">{isLogin ? "Masuk untuk melanjutkan ke obrolan" : "Mulai pengalaman chatting real-time"}</p>
	</div>

	<div class="mt-8 sm:mx-auto sm:w-full sm:max-w-md z-10 px-4 sm:px-0">
		<div class="bg-white py-8 px-6 shadow-xl border border-slate-100 rounded-3xl sm:px-10">
			{#if errorMessage}
				<div class="mb-5 p-4 bg-rose-50 border border-rose-100 text-rose-700 rounded-2xl text-xs sm:text-sm flex items-center space-x-2">
					<svg class="w-5 h-5 shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 9v3.75m9-.75a9 9 0 1 1-18 0 9 9 0 0 1 18 0Zm-9 3.75h.008v.008H12v-.008Z"/></svg>
					<span>{errorMessage}</span>
				</div>
			{/if}

			{#if successMessage}
				<div class="mb-5 p-4 bg-teal-50 border border-teal-100 text-teal-700 rounded-2xl text-xs sm:text-sm flex items-center space-x-2">
					<svg class="w-5 h-5 shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12.75 11.25 15 15 9.75M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Z"/></svg>
					<span>{successMessage}</span>
				</div>
			{/if}

			<form on:submit={handleAuth} class="space-y-5">
				{#if !isLogin}
					<InputField id="username" label="Username" placeholder="username_kamu" bind:value={username} required>
						<svg slot="icon" class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 1 1-7.5 0 3.75 3.75 0 0 1 7.5 0ZM4.501 20.118a7.5 7.5 0 0 1 14.998 0A17.933 17.933 0 0 1 12 21.75c-2.676 0-5.216-.584-7.499-1.632Z"/></svg>
					</InputField>
				{/if}

				<InputField id="email" type="email" label="Alamat Email" placeholder="nama@email.com" bind:value={email} required>
					<svg slot="icon" class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M21.75 6.75v10.5a2.25 2.25 0 0 1-2.25 2.25H4.5a2.25 2.25 0 0 1-2.25-2.25V6.75m19.5 0A2.25 2.25 0 0 0 19.5 4.5h-15a2.25 2.25 0 0 0-2.25 2.25m19.5 0v.243a2.25 2.25 0 0 1-1.07 1.916l-7.5 4.615a2.25 2.25 0 0 1-2.36 0L3.32 8.91a2.25 2.25 0 0 1-1.07-1.916V6.75"/></svg>
				</InputField>

				<InputField id="password" type="password" label="Password" placeholder="••••••••" bind:value={password} required>
					<svg slot="icon" class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M16.5 10.5V6.75a4.5 4.5 0 1 0-9 0v3.75m-.75 11.25h10.5a2.25 2.25 0 0 0 2.25-2.25v-6.75a2.25 2.25 0 0 0-2.25-2.25H6.75a2.25 2.25 0 0 0-2.25 2.25v6.75a2.25 2.25 0 0 0 2.25 2.25Z"/></svg>
				</InputField>

				{#if isLogin}
					<div class="flex items-center justify-end"><a href="/auth/reset-password" class="text-sm font-semibold text-blue-600 hover:text-blue-500 transition">Lupa password?</a></div>
				{/if}

				<button type="submit" disabled={loading} class="w-full flex justify-center py-3.5 px-4 rounded-xl shadow-lg shadow-blue-200 text-sm font-bold text-white bg-linear-to-r from-blue-600 to-indigo-600 hover:from-blue-700 hover:to-indigo-700 transition disabled:opacity-50">
					{#if loading}
						<svg class="animate-spin -ml-1 mr-2.5 h-5 w-5 text-white" fill="none" viewBox="0 0 24 24"><circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle><path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
						<span>Memproses...</span>
					{:else}
						<span>{isLogin ? "Masuk Sekarang" : "Buat Akun Gratis"}</span>
					{/if}
				</button>
			</form>

			<div class="mt-6 text-center">
				<div class="relative flex justify-center text-xs sm:text-sm mb-4">
					<div class="absolute inset-0 flex items-center"><div class="w-full border-t border-slate-200"></div></div>
					<span class="px-2.5 bg-white text-slate-500 relative">{isLogin ? "Belum mempunyai akun?" : "Sudah mempunyai akun?"}</span>
				</div>
				<button type="button" on:click={toggleMode} class="w-full py-3 border border-slate-250 rounded-xl text-sm font-semibold text-slate-700 hover:bg-slate-50 transition">
					{isLogin ? "Daftar di sini" : "Masuk di sini"}
				</button>
			</div>
		</div>
	</div>
</div>