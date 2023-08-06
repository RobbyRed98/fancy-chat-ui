<script lang="ts">
    import { currentUser, pb } from "./pocketbase";
    let username: string;
    let password: string;
    async function login() {
        await pb.collection('users').authWithPassword(username, password, {}, {expand: "pool"});
    }

    let showLogoutText = false;
    async function logout() {
        pb.authStore.clear()
        showLogoutText = false
    }

    async function makeLogoutTextShow() {
      showLogoutText = true;
    }
    async function hideLogoutTextShow() {
      showLogoutText = false;
    }
</script>

{#if $currentUser}
<div class="header py-4 text-white text-center">
  <h1 class="text-3xl font-semibold">Fancy Chat</h1>
  <div class="flex items-center justify-end px-4 mt-2">
    <button class="relative bg-red-500 px-8 py-3 rounded-lg text-white text-sm" on:click={logout} on:mouseenter={makeLogoutTextShow} on:mouseleave={hideLogoutTextShow}>
      <span class="inline" style="{`opacity: ${showLogoutText ? 0 : 1}`}">{$currentUser.username}</span>
      <span class="absolute top-0 left-0 right-0 bottom-0 flex items-center justify-center transition-opacity" style="{`opacity: ${showLogoutText ? 1 : 0}`}">
        Logout
      </span>
    </button>
  </div>
</div>
{:else}
<div class="header py-4 text-white text-center">
  <h1 class="text-3xl font-semibold">Fancy Chat</h1>
</div>
<div class="flex items-center justify-center max-h-screen">
  <form class="w-64 p-4 bg-gray-900 rounded-lg border border-gray-600" on:submit|preventDefault>
    <div class="mb-4">
      <input
        class="w-full px-3 py-2 bg-gray-800 border rounded border-gray-600 text-white placeholder-gray-500 focus:outline-none focus:border-blue-500"
        type="text"
        required
        placeholder="Username"
        bind:value={username}
      />
    </div>
    <div class="mb-4">
      <input
        class="w-full px-3 py-2 bg-gray-800 border rounded border-gray-600 text-white placeholder-gray-500 focus:outline-none focus:border-blue-500
        shadow-inner-neo"
        type="password"
        required
        placeholder="Password"
        bind:value={password}
      />
    </div>
    <div class="flex">
      <button
        class="flex-1 px-4 py-2 bg-blue-500 rounded-lg hover:bg-blue-600"
        on:click={login}
      >
        Login
      </button>
    </div>
  </form>
</div>  
{/if}

  
  
  
  
  
  