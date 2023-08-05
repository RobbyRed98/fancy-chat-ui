<script lang="ts">
    import { onMount, onDestroy } from 'svelte'
    import { currentUser, pb } from './pocketbase'

    let messages = [];
    let newMessage: string;
    let unsubscribe: () => void;

    onMount(async () => {
        const resultList = await pb.collection("messages").getList(1, 50, {sort: '-created', expand: 'user'});
        messages = resultList.items;

        unsubscribe = await pb.collection("messages").subscribe("*", async ({action, record}) => {
            if (action === 'create') {
                const user = await pb.collection('users').getOne(record.user);
                console.log("Fetched user: %s", user.username);
                record.expand = { user };
                messages = [record, ...messages];
                console.log(messages)
            } else if (action === 'delete') {
                messages.filter((msg) => msg.id !== record.id);
            }
        });
    });

    onDestroy(() => {
        unsubscribe();
    })

    async function sendMessage() {
        const data = {
            text: newMessage,
            user: $currentUser.id,
            pool: $currentUser.pool,
        };
        const createdMessage = await pb.collection("messages").create(data);
        newMessage = "";
    }
</script>
<div class="container">
    <div class="messages" id="message-container">
        {#each messages as message (message.id)}
            <div class="message">
                <small>@{message.expand?.user?.username}</small>
                <br>
                <span class="message-text">{message.text}</span>
            </div>
        {/each}
    </div>
    
    <div>
        <form class="chat" on:submit|preventDefault={sendMessage}>
            <input placeholder="Message" required type="text" bind:value={newMessage} />
            <button type="submit">Send</button>
        </form>
    </div>
</div>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: Arial, sans-serif;
}

/* Die gesamte Container-Div */
.container {
  max-width: 600px;
  margin: 0 auto;
  background-color: lightgrey;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
}

/* Die Nachrichten-Spalte */
.messages {
  max-height: 500px;
  border-radius: 6px;
  flex: 1;
  padding: 5px;
  overflow-y: auto;
  background-color: lightgrey;
}

/* Eine einzelne Nachricht */
.message {
  background-color: grey;
  padding: 4px 4px 4px 4px;
  border-radius: 5px;
  margin-bottom: 8px;
  position: relative;
  text-align: left;
}

.message .message-text {
  display: block; /* Text als Blockelement, um Zeilenumbruch zu erzwingen */
  text-align: left; /* Text rechtsbündig */
  word-wrap: break-word;
}

.message small {
  position: absolute;
  top: 5px;
  right: 5px;
  font-size: 12px;
  color: #555;
  background-color: rgba(255, 255, 255, 0.7); /* Transparenter Hintergrund */
  padding: 2px 4px; /* Kleiner Padding für etwas Abstand zum Text */
  border-radius: 3px;
}

.message span {
  display: block; /* Text als Blockelement, um Zeilenumbruch zu erzwingen */
}

.message:last-child {
  margin-bottom: 0;
}

/* Der Chat-Bereich */
.chat {
  padding: 10px;
  display: flex;
  align-items: center;
}

/* Das Eingabefeld im Chat */
.chat input {
  flex: 1;
  padding: 10px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  outline: none;
  background-color: #f7f7f7;
  color: black
}

/* Der Button im Chat */
.chat button {
  margin-left: 10px;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  color: #fff;
  background-color: #007bff;
  cursor: pointer;
}

/* Stil für die Scrollbar des Nachrichten-Containers */
#message-container::-webkit-scrollbar {
  width: 6px;
}

#message-container::-webkit-scrollbar-thumb {
  background-color: #ccc;
  border-radius: 5px;
}

#message-container::-webkit-scrollbar-thumb:hover {
  background-color: #b3b3b3;
}
    </style>
  