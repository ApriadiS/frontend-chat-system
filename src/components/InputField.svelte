<script lang="ts">
	export let id: string;
	export let type: "text" | "email" | "password" = "text";
	export let label: string;
	export let placeholder: string = "";
	export let value: string = "";
	export let required: boolean = false;
</script>

<div>
	<label for={id} class="block text-sm font-semibold text-slate-700 mb-1.5">
		{label}
	</label>
	<div class="relative rounded-xl shadow-sm">
		<div class="absolute inset-y-0 left-0 pl-3.5 flex items-center pointer-events-none text-slate-400">
			<slot name="icon"></slot>
		</div>
		<input
			{id}
			{type}
			bind:value
			{required}
			{placeholder}
			autocomplete={type === "password" ? "current-password" : "off"}
			autocapitalize="none"
			autocorrect="off"
			class="block w-full pl-11 pr-4 py-3 sm:py-2.5 border border-slate-200 rounded-xl bg-slate-50/50 focus:bg-white text-slate-800 text-base sm:text-sm focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition focus:outline-none"
		/>
	</div>
</div>