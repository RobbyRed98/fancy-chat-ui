<script lang="ts">
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Login from './lib/Login.svelte'
  import Messages from './lib/Messages.svelte';
</script>

<main>
  <h1>Fancy Chat</h1>
  <Login />
  <Messages />
</main>

<style>
  .msg {
    width:500px;
    height:300px;
    background:lightGrey; 
    border-radius: 10px 10px 10px 10px;
    overflow:hidden;
  }
/* Die gesamte Container-Div */
.container {
  max-width: 100%;
  box-sizing: border-box;
  padding: 10px;
  display: flex;
  flex-direction: column;
  height: 400px; /* Eine feste Höhe, damit die Scrollbar funktioniert */
  position: relative; /* Positionseinstellung für den eingabe Container */
}

/* Die Nachrichten-Spalte */
.messages {
  background-color: #f1f1f1;
  flex: 1; /* Die Nachrichten-Spalte nimmt den größten verfügbaren Platz ein */
  padding: 10px;
  overflow-y: auto; /* Scrollbar bei Bedarf anzeigen */
}

/* Der Chat-Bereich */
.chat {
  background-color: #f1f1f1;
  padding: 10px;
  position: absolute;
  bottom: 0; /* Der Eingabe-Container wird unten fixiert */
  left: 0;
  width: 100%;
}

/* Das Eingabefeld im Chat */
.chat input {
  width: 100%;
  padding: 10px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
  outline: none;
}
</style>
