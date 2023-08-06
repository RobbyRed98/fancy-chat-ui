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

    function toDateTime(date: string) {
      let dateObject = new Date(date);
      return `${dateObject.getHours()}:${dateObject.getMinutes()} - ${dateObject.getDate()}.${dateObject.getMonth()+1}.${dateObject.getFullYear()}`;
    }
</script>

<style>
  /* Stil für die Scrollbar des Nachrichten-Containers */
  .messages::-webkit-scrollbar {
    width: 8px;
  }

  .messages::-webkit-scrollbar-thumb {
    background-color: #ccc;
    border-radius: 5px;
  }

  .messages::-webkit-scrollbar-thumb:hover {
    background-color: #b3b3b3;
  }
</style>

{#if $currentUser}
<div class="container max-w-2xl mx-auto rounded-lg p-4 flex flex-col">
  <div class="messages max-h-96 overflow-y-auto mb-4 rounded-lg">
    {#each messages as message (message.id)}
    <div class="message p-4 mb-8 relative text-left rounded-lg bg-gray-600">
      <small class="absolute top-1 left-1 text-sm text-gray-400 bg-opacity-70 rounded-sm p-1">
        {toDateTime(message.created)}
      </small>
      <small class="absolute top-1 right-1 text-sm text-gray-400 bg-opacity-70 rounded-sm p-1">
        @{message.expand?.user?.username}
      </small>
      <br/>
      <span class="block break-words text-white">{message.text}</span>
    </div>
    {/each}
  </div>
  <div class="flex-1"></div>
  <div class="chat flex items-center p-4 max-w-2xl mx-auto">
    <form on:submit|preventDefault={sendMessage}>
      <input
        class="flex-1 px-4 py-2 bg-gray-200 rounded-lg text-black focus:outline-none"
        placeholder="Message"
        required
        type="text"
        bind:value={newMessage}
      />
      <button
        class="ml-4 px-4 py-2 bg-blue-500 rounded-lg hover:bg-blue-600 text-white"
        type="submit"
      >
        Send
      </button>
    </form>
  </div>
</div>
{/if}