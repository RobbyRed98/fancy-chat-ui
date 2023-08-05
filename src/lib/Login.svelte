<script lang="ts">
    import { currentUser, pb } from "./pocketbase";
    let username: string;
    let password: string;
    async function login() {
        await pb.collection('users').authWithPassword(username, password);
    }
    
    async function signUp() {
        try {
            const data = {
                username,
                password,
                passwordConfirm: password,
            }
            const createdUser = await pb.collection('users').create(data);
            await login();
        } catch (err) {
            console.error(err)
        }
    }

    async function signOut() {
        pb.authStore.clear()
    }

</script>

{#if $currentUser}
    <p>Signed in as {$currentUser.username}</p>
{:else}
    <div class="login-container">
        <form on:submit|preventDefault>
        <div class="form-group">
            <input placeholder="Username" type="text" bind:value={username} />
        </div>
        <div class="form-group">
            <input placeholder="Password" type="password" bind:value={password} />
        </div>
        <div class="buttons">
            <button on:click={signUp}>Sign Up</button>
            <button on:click={login}>Login</button>
        </div>
        </form>
    </div>
{/if}

<style>
    .login-container {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      margin-bottom: 20px;
    }
  
    .form-group {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-bottom: 10px;
    }
  
    .form-group input {
      width: 200px;
      padding: 10px;
      margin-bottom: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
  
    .buttons {
      display: flex;
    }
  
    .buttons button {
      padding: 10px 20px;
      margin-right: 10px;
      border: none;
      border-radius: 5px;
      font-size: 16px;
      color: #fff;
      background-color: #007bff;
      cursor: pointer;
    }
</style>
  
  
  
  
  
  