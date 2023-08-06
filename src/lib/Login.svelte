<script lang="ts">
    import { currentUser, pb } from "./pocketbase";
    let username: string;
    let password: string;
    async function login() {
        await pb.collection('users').authWithPassword(username, password, {}, {expand: "pool"});
    }

    async function logout() {
        pb.authStore.clear()
    }

</script>

{#if $currentUser}
  <p>Signed in as {$currentUser.username}</p>
  <button
    class="flex-1 px-4 py-2 bg-blue-500 rounded-lg hover:bg-blue-600"
    on:click={logout}
  >Logout</button>
{:else}
  <div class="flex items-center justify-center min-h-screen bg-gray-800">
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

  
  
  
  
  
  