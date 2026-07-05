<script lang="ts">
	import { createEventDispatcher } from "svelte";
	const dispatch = createEventDispatcher();

	export let viewMode: "sidebar" | "chat";
	export let currentUsername: string;
	export let chatType: string;
	export let selectedTargetId: string;
	export let unreadBadges: Record<string, boolean>;
	export let usersList: any[];
	export let groupsList: any[];
	export let currentGroupMemberIds: string[];

	let newGroupName = "";

	const switchChat = (type: string, id: string, name: string) => dispatch("switchChat", { type, id, name });
	const createGroup = () => {
		if (newGroupName.trim()) {
			dispatch("createGroup", newGroupName.trim());
			newGroupName = "";
		}
	};
</script>

<aside class="w-full md:w-80 bg-white border-r border-slate-200 flex flex-col shrink-0 transition-all duration-300 {viewMode === 'chat' ? 'hidden md:flex' : 'flex'}">
	<div class="p-4 border-b border-slate-200 bg-slate-50/50 flex items-center justify-between">
		<h1 class="font-extrabold text-xl bg-linear-to-r from-blue-600 to-indigo-600 bg-clip-text text-transparent flex items-center gap-2">
			<span>💬 ChatMVP</span>
		</h1>
	</div>

	<div class="flex-1 p-4 overflow-y-auto space-y-5">
		<div>
			<p class="text-[10px] text-slate-400 font-bold uppercase tracking-wider mb-2.5">Umum</p>
			<button on:click={() => switchChat("broadcast", "", "Semua Orang")} class="w-full text-left p-3 text-sm rounded-xl font-semibold transition flex justify-between items-center group {chatType === 'broadcast' ? 'bg-blue-50 text-blue-700 border-blue-100/50' : 'text-slate-600 hover:bg-slate-50 border-transparent'}">
				<div class="flex items-center space-x-2.5">
					<span>📢 Broadcast Room</span>
				</div>
				{#if unreadBadges["broadcast"]}<span class="w-2 h-2 bg-rose-500 rounded-full animate-pulse"></span>{/if}
			</button>
		</div>

		<div>
			<p class="text-[10px] text-slate-400 font-bold uppercase tracking-wider mb-2.5">Kontak (1-to-1)</p>
			<div class="space-y-1.5 max-h-56 overflow-y-auto pr-1">
				{#each usersList as u}
					<div class="flex items-center justify-between rounded-xl border border-transparent hover:border-slate-150 hover:bg-slate-50 transition">
						<button on:click={() => switchChat("private", u.id, u.username)} class="text-left py-3 px-2 md:py-2 text-sm truncate flex-1 flex justify-between items-center font-medium {chatType === 'private' && selectedTargetId === u.id ? 'text-blue-700 font-bold' : 'text-slate-600'}">
							<div class="flex items-center space-x-2 truncate">
								<div class="w-7 h-7 rounded-lg bg-blue-100 text-blue-700 flex items-center justify-center font-bold text-xs uppercase shrink-0">{u.username.substring(0, 2)}</div>
								<span class="truncate">@{u.username}</span>
							</div>
							{#if unreadBadges[`private:${u.id}`]}<span class="w-1.5 h-1.5 bg-rose-500 rounded-full"></span>{/if}
						</button>
						{#if chatType === "group" && !currentGroupMemberIds.includes(u.id)}
							<button on:click={() => dispatch("inviteToGroup", u.id)} class="mx-2 px-2 py-1.5 text-[10px] bg-emerald-50 text-emerald-700 rounded-lg font-bold hover:bg-emerald-100">+ Undang</button>
						{/if}
					</div>
				{/each}
			</div>
		</div>

		<div>
			<p class="text-[10px] text-slate-400 font-bold uppercase tracking-wider mb-2.5">Grup Saya</p>
			<div class="space-y-1.5 mb-3 max-h-48 overflow-y-auto pr-1">
				{#each groupsList as g}
					<button on:click={() => switchChat("group", g.id, g.name)} class="w-full text-left py-3 px-2 md:py-2.5 text-sm rounded-xl transition truncate flex justify-between items-center font-medium {chatType === 'group' && selectedTargetId === g.id ? 'bg-blue-50 text-blue-700 font-bold' : 'text-slate-600 hover:bg-slate-50'}">
						<div class="flex items-center space-x-2 truncate">
							<div class="w-7 h-7 rounded-lg bg-indigo-100 text-indigo-700 flex items-center justify-center font-bold text-xs uppercase shrink-0">{g.name.substring(0, 2)}</div>
							<span class="truncate">{g.name}</span>
						</div>
						{#if unreadBadges[`group:${g.id}`]}<span class="w-1.5 h-1.5 bg-rose-500 rounded-full"></span>{/if}
					</button>
				{/each}
			</div>

			<div class="pt-2.5 border-t border-slate-150 flex gap-1.5">
				<input type="text" bind:value={newGroupName} placeholder="Nama grup baru..." class="flex-1 px-3 py-2 md:py-1.5 text-base md:text-xs border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-blue-500 bg-slate-50" />
				<button on:click={createGroup} class="px-3 py-1.5 bg-blue-600 hover:bg-blue-700 text-white rounded-xl font-bold">+</button>
			</div>
		</div>
	</div>

	<div class="p-4 border-t border-slate-200 bg-slate-50/80">
		<div class="flex items-center space-x-3 mb-4">
			<div class="w-10 h-10 rounded-xl bg-blue-600 text-white flex items-center justify-center font-black text-sm uppercase">{currentUsername.substring(0, 2)}</div>
			<div class="truncate flex-1">
				<p class="text-[10px] text-slate-400 font-bold uppercase tracking-wider">Login Sebagai</p>
				<p class="text-sm font-bold text-slate-800 truncate">@{currentUsername}</p>
			</div>
		</div>
		<button on:click={() => dispatch("logout")} class="w-full py-2.5 text-xs text-rose-600 font-semibold bg-rose-50 hover:bg-rose-100 border rounded-xl">Keluar Akun</button>
	</div>
</aside>