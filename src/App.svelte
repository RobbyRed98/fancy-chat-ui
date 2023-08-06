<script lang="ts">
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Login from './lib/Login.svelte'
  import Messages from './lib/Messages.svelte';
  import TailwindCss from './lib/TailwindCSS.svelte';
</script>

<TailwindCss/>
<main>
  <Login />
  <Messages />
</main>
