<script>
  import { Router, Route } from "svelte-routing";
  
  import Landing from "./pages/Landing.svelte";
  import Auth from "./pages/Auth.svelte";
  import ResetPassword from "./pages/ResetPassword.svelte";
  import Dashboard from "./pages/Dashboard.svelte";

  export let url = "";
</script>

<Router {url}>
  <main class="min-h-screen bg-gray-50 text-gray-900">
    <Route path="/">
      <Landing />
    </Route>
    
    <Route path="/auth">
      <Auth />
    </Route>
    
    <Route path="/auth/reset-password">
      <ResetPassword />
    </Route>
    
    <Route path="/dashboard">
      <Dashboard />
    </Route>
  </main>
</Router>