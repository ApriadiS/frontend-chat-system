<script lang="ts">
	export let msg: any;
	export let isMe: boolean;
	export let chatType: string;

	$: initial = msg.isSystem ? "S" : (msg.sender ? msg.sender.charAt(0).toUpperCase() : "?");
	
	$: avatarBg = msg.isSystem 
		? "bg-slate-200 text-slate-600" 
		: (isMe ? "bg-blue-100 text-blue-700" : "bg-amber-100 text-amber-700");
	
	$: senderNameColor = msg.isSystem ? "text-blue-600" : "text-amber-600";
</script>

<div class="flex items-start space-x-2.5 w-full {isMe ? 'justify-end' : ''}">
	
	{#if !isMe}
		<div class="w-8 h-8 rounded-full flex items-center justify-center text-[11px] font-bold shrink-0 {avatarBg}">
			{initial}
		</div>
	{/if}

	<div class="p-2.5 rounded-xl shadow-sm max-w-[85%] md:max-w-[75%] relative flex flex-col
		{isMe ? 'bg-blue-600 text-white rounded-tr-none' : 'bg-white border border-slate-100 rounded-tl-none'}">
		
		{#if !isMe && (chatType === "group" || chatType === "broadcast" || msg.isSystem)}
			<p class="text-[10px] font-bold mb-0.5 {senderNameColor}">
				{msg.isSystem ? "System" : msg.sender}
			</p>
		{/if}

		<p class="text-sm md:text-[13px] leading-relaxed wrap-break-word whitespace-pre-wrap pr-9 
			{isMe ? 'text-white' : 'text-slate-600'}">
			{msg.content}
		</p>

		<span class="text-[9px] self-end absolute bottom-1 right-2 font-mono 
			{isMe ? 'text-blue-200' : 'text-slate-400'}">
			{msg.timestamp || "00:00"}
		</span>
	</div>

	{#if isMe}
		<div class="w-8 h-8 rounded-full flex items-center justify-center text-[11px] font-bold shrink-0 {avatarBg}">
			{initial}
		</div>
	{/if}
</div>