<script lang="ts">
	import { createEventDispatcher, tick } from "svelte";
	import MessageBubble from "./MessageBubble.svelte";

	const dispatch = createEventDispatcher();

	export let viewMode: "sidebar" | "chat";
	export let chatType: string;
	export let selectedTargetName: string;
	export let currentGroupMemberIds: string[];
	export let messages: string[] = [];
	export let currentUsername: string;

	let messageText = "";
	let chatContainer: HTMLDivElement;

	// Mendefinisikan tipe eksplisit untuk menenangkan TypeScript
	type ParsedMsg = { dateStr: string; isSystem: boolean; content: string; timestamp: string; sender: string };

	// Array reaktif dengan Type Guard memastikan array bersih dari null
	$: parsedMessages = messages.map(parseMessage).filter((m): m is ParsedMsg => m !== null);

	function parseMessage(rawStr: string): ParsedMsg | null {
		if (!rawStr) return null;
		
		let dateStr = "";
		let msgStr = rawStr;
		const pipeIdx = rawStr.indexOf('|');
		
		if (pipeIdx !== -1) {
			dateStr = rawStr.substring(0, pipeIdx);
			msgStr = rawStr.substring(pipeIdx + 1);
		}

		let result = { dateStr, isSystem: false, content: msgStr, timestamp: "", sender: "" };

		if (msgStr.startsWith("[SYSTEM]")) {
			result.isSystem = true;
			result.content = msgStr.replace("[SYSTEM]", "").trim();
			result.sender = "SYSTEM";
			return result;
		}
		const systemMatch = msgStr.match(/^\[(\d{2}[:\.]\d{2}[:\.]\d{2})\]\s*\*\*\*(.*)\*\*\*$/);
		if (systemMatch) {
			result.isSystem = true;
			result.content = systemMatch[2].trim();
			result.timestamp = systemMatch[1].replace(/\./g, ":");
			result.sender = "SYSTEM";
			return result;
		}
		const textMatch = msgStr.match(/^\[(\d{2}[:\.]\d{2}[:\.]\d{2})\]\s*([^:]+):\s*(.*)$/);
		if (textMatch) {
			result.timestamp = textMatch[1].replace(/\./g, ":");
			result.sender = textMatch[2].trim();
			result.content = textMatch[3].trim();
			return result;
		}
		return result;
	}

	function formatDateBubble(dateStr: string) {
		if (!dateStr) return "Hari Ini"; 
		
		const msgDate = new Date(dateStr);
		const today = new Date();
		
		const yesterday = new Date(today);
		yesterday.setDate(yesterday.getDate() - 1);

		if (msgDate.toDateString() === today.toDateString()) return "Hari Ini";
		if (msgDate.toDateString() === yesterday.toDateString()) return "Kemarin";
		
		return msgDate.toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' });
	}

	async function scrollToBottom() {
		await tick();
		if (chatContainer) chatContainer.scrollTo({ top: chatContainer.scrollHeight, behavior: "smooth" });
	}

	$: if (messages) scrollToBottom();

	const handleSend = (e: Event) => {
		e.preventDefault();
		if (!messageText.trim()) return;
		dispatch("sendMessage", messageText.trim());
		messageText = "";
	};
</script>

<main class="flex-1 flex flex-col bg-[#efeae2] relative {viewMode === 'sidebar' ? 'hidden md:flex' : 'flex'}">
	<div class="p-4 border-b border-slate-200 bg-white shadow-sm flex items-center justify-between">
		<div class="flex items-center">
			<button aria-label="Kembali ke Sidebar" on:click={() => dispatch("backToSidebar")} class="md:hidden mr-3 p-1.5 rounded-lg text-slate-500 hover:bg-slate-100">
				<svg class="w-6 h-6" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5 8.25 12l7.5-7.5" /></svg>
			</button>
			<div>
				<h2 class="font-bold text-slate-800 text-sm sm:text-base flex items-center gap-1.5">
					<span>{chatType === "broadcast" ? "📢" : chatType === "group" ? "👥" : "👤"}</span>
					<span class="truncate max-w-xs">{selectedTargetName}</span>
				</h2>
				<p class="text-[10px] text-slate-400 font-medium capitalize mt-0.5">Tipe: {chatType}</p>
			</div>
		</div>
		{#if chatType === "group"}
			<span class="text-[10px] bg-slate-100 text-slate-500 px-2.5 py-1 rounded-lg border font-mono font-semibold">
				Anggota: {currentGroupMemberIds.length}
			</span>
		{/if}
	</div>

	<div bind:this={chatContainer} class="flex-1 p-4 overflow-y-auto space-y-3 flex flex-col">
		{#if parsedMessages.length === 0}
			<div class="my-auto flex flex-col items-center justify-center text-slate-400 space-y-2">
				<div class="text-xs font-semibold">Tidak ada pesan di sini. Mulai obrolan pertamamu!</div>
			</div>
		{:else}
			{#each parsedMessages as msg, i}
				{@const showDate = i === 0 || msg.dateStr !== parsedMessages[i-1]?.dateStr}
				
				{#if showDate}
					<div class="flex justify-center my-3">
						<span class="px-3.5 py-1 bg-white/95 backdrop-blur shadow-sm rounded-lg text-[10px] font-bold text-slate-500 uppercase tracking-wider border border-slate-100">
							{formatDateBubble(msg.dateStr)}
						</span>
					</div>
				{/if}
				
				<MessageBubble {msg} {chatType} isMe={msg.sender === currentUsername} />
			{/each}
		{/if}
	</div>

	<div class="p-3 sm:p-4 bg-white border-t border-slate-200 pb-4 md:pb-4">
		<form on:submit={handleSend} class="flex gap-2">
			<input type="text" bind:value={messageText} placeholder="Ketik pesan..." autocomplete="off" class="flex-1 px-4 py-3 md:py-2.5 border border-slate-200 bg-slate-50/50 focus:bg-white rounded-xl focus:outline-none focus:ring-2 focus:ring-blue-500 transition text-base md:text-sm text-slate-800" />
			<button type="submit" class="px-5 py-2.5 bg-blue-600 text-white font-bold rounded-xl hover:bg-blue-700 transition text-sm shadow-md flex items-center">Kirim</button>
		</form>
	</div>
</main>