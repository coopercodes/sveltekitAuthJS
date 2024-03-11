<script lang="ts">
    import { signIn, signOut } from "@auth/sveltekit/client"
    // signIn() -> run the logic to sign in user, signIn("github") -> use the sign in with github logic
    // signOut() -> run the logic to sign out user
    import { page } from "$app/stores"
    // $page.data.session -> { user, image, etc. } AUTH session
    console.log($page.data.session) 
    let followerList: any = [];

    async function getFollowerList() {
        await fetch("https://api.github.com/user/followers", {
            headers: {
                Accept: "application/vnd.github+json",
                //@ts-ignore
                Authorization: "Bearer " + $page.data.session?.access_token,
                "X-GitHub-Api-Version": "2022-11-28"
            }
        }).then((data) => {
            return data.json()
        }).then((data) => {
            console.log(data); // [ user1, user2, user3 ]
            followerList = data;
        })
    }
    console.log(followerList);
</script>


<div class="p-24">
    {#if $page.data.session}
        <h1>You are logged in</h1>
        {#if $page.data.session.user?.image}
            <img 
                src={$page.data.session.user.image}
                alt="User Profile"
                class="w-12 h-12"
            />
        {/if}
        <p>Signed in as {$page.data.session.user?.name}</p>
        <button on:click={() => signOut()} class="bg-blue-500 py-1 px-2 rounded text-white font-bold">Sign Out</button>
        <button on:click={() => getFollowerList()} class="bg-blue-800 py-1 px-2 rounded text-white font-bold">Get Followers List</button>
        <ul class="w-64">
            {#each followerList as item, index}
                <li id={"follower" + index} class="w-64">{item.login}</li>
            {/each}
        </ul>
    {:else}
        <h1>You are not logged in</h1>
        <button on:click={() => signIn("github")} class="bg-blue-500 py-1 px-2 rounded text-white font-bold">Sign in with GitHub</button>
    {/if}
</div>