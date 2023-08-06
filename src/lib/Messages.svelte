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

<div class="max-w-2xl mx-auto bg-lightgray rounded-lg p-4">
  <div class="messages max-h-96 overflow-y-auto bg-lightgray">
    {#each messages as message (message.id)}
    <div class="message bg-gray-600 p-4 mb-8 relative text-left rounded-lg">
      <small class="absolute top-1 right-1 text-sm text-gray-500 bg-opacity-70 rounded-sm p-1">
        @{message.expand?.user?.username}
      </small>
      <span class="block break-words text-white">{message.text}</span>
    </div>
    {/each}
  </div>

  <div>
    <form class="chat flex items-center p-4" on:submit|preventDefault={sendMessage}>
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
