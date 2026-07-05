<script lang="ts">
   import { onMount } from "svelte";
   import { navigate } from "svelte-routing";
   import { supabase } from "../lib/supabase";

   import Sidebar from "../components/Sidebar.svelte";
   import ChatArea from "../components/ChatArea.svelte";

   let loading = true;
   let currentUserId = "";
   let currentUsername = "";
   let socket: WebSocket | null = null;

   let chatStore: Record<string, string[]> = { broadcast: [] };
   let loadedHistories: Record<string, boolean> = {};
   let unreadBadges: Record<string, boolean> = {};

   let viewMode: "sidebar" | "chat" = "sidebar";
   let usersList: any[] = [];
   let groupsList: any[] = [];
   let currentGroupMemberIds: string[] = [];

   let chatType: "broadcast" | "private" | "group" = "broadcast";
   let selectedTargetId = "";
   let selectedTargetName = "Semua Orang";

   $: currentChatKey =
      chatType === "broadcast"
         ? "broadcast"
         : `${chatType}:${selectedTargetId}`;

   onMount(() => {
      const checkSession = async () => {
         const {
            data: { session },
            error,
         } = await supabase.auth.getSession();
         if (error || !session) {
            navigate("/auth", { replace: true });
         } else {
            currentUserId = session.user.id;
            const { data: profile } = await supabase
               .from("profiles")
               .select("username")
               .eq("id", currentUserId)
               .single();
            currentUsername = profile
               ? profile.username
               : session.user.email?.split("@")[0] || "";

            await loadSidebarData();
            await loadChatHistory("broadcast");
            loading = false;
            initWebSocket(currentUserId);
         }
      };

      checkSession();

      const initWebSocket = (userId: string) => {
         // Membaca URL WebSocket dari environment variable Vite
         const wsBaseUrl = import.meta.env.VITE_WS_URL;
         socket = new WebSocket(
            `${wsBaseUrl}?userId=${userId}&username=${currentUsername}`,
         );

         socket.onmessage = async (event) => {
            const raw = event.data;
            if (raw.startsWith("system_action:reload_sidebar")) {
               await loadSidebarData();
               return;
            }
            if (raw.startsWith("system:error:")) return; // Tangani error jika perlu

            const firstColon = raw.indexOf(":");
            const secondColon = raw.indexOf(":", firstColon + 1);
            if (firstColon === -1 || secondColon === -1) return;

            const msgType = raw.substring(0, firstColon);
            const msgTarget = raw.substring(firstColon + 1, secondColon);
            const formattedMsg = raw.substring(secondColon + 1);

            let targetStoreKey =
               msgType === "broadcast"
                  ? "broadcast"
                  : `${msgType}:${msgTarget}`;

            const todayStr = getLocalDateStr();
            if (!chatStore[targetStoreKey]) chatStore[targetStoreKey] = [];
            chatStore[targetStoreKey] = [
               ...chatStore[targetStoreKey],
               `${todayStr}|${formattedMsg}`,
            ];

            if (targetStoreKey !== currentChatKey)
               unreadBadges[targetStoreKey] = true;
         };
      };

      const { data: authListener } = supabase.auth.onAuthStateChange(
         (event, session) => {
            if (event === "SIGNED_OUT" || !session)
               navigate("/auth", { replace: true });
         },
      );

      return () => {
         authListener.subscription.unsubscribe();
         if (socket) socket.close();
      };
   });

   async function loadSidebarData() {
      const { data: users } = await supabase
         .from("profiles")
         .select("*")
         .neq("id", currentUserId);
      usersList = users || [];

      const { data: myGroups } = await supabase
         .from("group_members")
         .select("group_id, groups(id, name)")
         .eq("user_id", currentUserId);
      groupsList = myGroups
         ? myGroups.map((mg) => mg.groups).filter(Boolean)
         : [];
   }

   async function loadChatHistory(key = currentChatKey) {
      let query = supabase
         .from("messages")
         .select("*, sender:profiles!messages_sender_id_fkey(username)")
         .order("created_at", { ascending: true });

      let qType = key === "broadcast" ? "broadcast" : key.split(":")[0];
      let qTargetId = key === "broadcast" ? "" : key.split(":")[1];

      if (qType === "broadcast") query = query.eq("chat_type", "broadcast");
      else if (qType === "private")
         query = query
            .eq("chat_type", "private")
            .or(
               `and(sender_id.eq.${currentUserId},receiver_id.eq.${qTargetId}),and(sender_id.eq.${qTargetId},receiver_id.eq.${currentUserId})`,
            );
      else if (qType === "group")
         query = query.eq("chat_type", "group").eq("group_id", qTargetId);

      const { data } = await query;
      if (data) {
         chatStore[key] = data.map((m) => {
            const dateObj = new Date(m.created_at);
            const dateStr = getLocalDateStr(dateObj);
            const time = dateObj
               .toLocaleTimeString("id-ID", { hour12: false })
               .replace(/\./g, ":");
            return `${dateStr}|[${time}] ${m.sender ? m.sender.username : "User"}: ${m.content}`;
         });
         loadedHistories[key] = true;
      }
   }

   async function switchChat(
      type: any,
      targetId = "",
      targetName = "Semua Orang",
   ) {
      chatType = type;
      selectedTargetId = targetId;
      selectedTargetName = targetName;
      viewMode = "chat";

      const newChatKey =
         type === "broadcast" ? "broadcast" : `${type}:${targetId}`;
      unreadBadges[newChatKey] = false;

      if (type === "group") {
         const { data: members } = await supabase
            .from("group_members")
            .select("user_id")
            .eq("group_id", targetId);
         currentGroupMemberIds = members ? members.map((m) => m.user_id) : [];
      } else {
         currentGroupMemberIds = [];
      }

      if (!loadedHistories[newChatKey]) await loadChatHistory(newChatKey);
   }

   async function handleCreateGroup(groupName: string) {
      const { data: groupData } = await supabase
         .from("groups")
         .insert({ name: groupName, created_by: currentUserId })
         .select()
         .single();
      if (groupData) {
         await supabase
            .from("group_members")
            .insert({ group_id: groupData.id, user_id: currentUserId });
         await loadSidebarData();
         await switchChat("group", groupData.id, groupData.name);
         triggerSidebarReload([currentUserId]);
      }
   }

   async function inviteToGroup(targetUserId: string) {
      const { error } = await supabase
         .from("group_members")
         .insert({ group_id: selectedTargetId, user_id: targetUserId });
      if (!error) {
         alert("Berhasil menambahkan anggota baru!");
         const { data: members } = await supabase
            .from("group_members")
            .select("user_id")
            .eq("group_id", selectedTargetId);
         currentGroupMemberIds = members ? members.map((m) => m.user_id) : [];
         triggerSidebarReload(currentGroupMemberIds);
      }
   }

   function triggerSidebarReload(targetMemberIds: string[]) {
      if (socket && socket.readyState === WebSocket.OPEN) {
         socket.send(
            JSON.stringify({
               chat_type: "system_action",
               receiver_id: "reload_sidebar",
               content: "",
               member_ids: targetMemberIds,
            }),
         );
      }
   }

   function getLocalDateStr(dateObj = new Date()) {
      const y = dateObj.getFullYear();
      const m = String(dateObj.getMonth() + 1).padStart(2, "0");
      const d = String(dateObj.getDate()).padStart(2, "0");
      return `${y}-${m}-${d}`;
   }

   const handleSendMessage = async (content: string) => {
      const payload = {
         chat_type: chatType,
         receiver_id: chatType === "broadcast" ? "" : selectedTargetId,
         content,
         member_ids: chatType === "group" ? currentGroupMemberIds : [],
      };

      await supabase.from("messages").insert({
         sender_id: currentUserId,
         receiver_id: chatType === "private" ? selectedTargetId : null,
         group_id: chatType === "group" ? selectedTargetId : null,
         content,
         chat_type: chatType,
      });

      if (socket && socket.readyState === WebSocket.OPEN)
         socket.send(JSON.stringify(payload));

      const now = new Date();
      const localTime = now
         .toLocaleTimeString("id-ID", { hour12: false })
         .replace(/\./g, ":");
      const todayStr = getLocalDateStr(now);

      if (!chatStore[currentChatKey]) chatStore[currentChatKey] = [];
      chatStore[currentChatKey] = [
         ...chatStore[currentChatKey],
         `${todayStr}|[${localTime}] ${currentUsername}: ${content}`,
      ];
   };
</script>

{#if loading}
   <div class="flex items-center justify-center min-h-screen bg-slate-50">
      <p class="text-slate-500 font-semibold animate-pulse">
         Memuat dashboard...
      </p>
   </div>
{:else}
   <div class="flex h-screen bg-slate-100 overflow-hidden font-sans">
      <Sidebar
         {viewMode}
         {currentUsername}
         {chatType}
         {selectedTargetId}
         {unreadBadges}
         {usersList}
         {groupsList}
         {currentGroupMemberIds}
         on:switchChat={(e) =>
            switchChat(e.detail.type, e.detail.id, e.detail.name)}
         on:createGroup={(e) => handleCreateGroup(e.detail)}
         on:inviteToGroup={(e) => inviteToGroup(e.detail)}
         on:logout={() => supabase.auth.signOut()}
      />

      <ChatArea
         {viewMode}
         {chatType}
         {selectedTargetName}
         {currentGroupMemberIds}
         {currentUsername}
         messages={chatStore[currentChatKey] || []}
         on:backToSidebar={() => (viewMode = "sidebar")}
         on:sendMessage={(e) => handleSendMessage(e.detail)}
      />
   </div>
{/if}
