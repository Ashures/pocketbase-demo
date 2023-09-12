<script>
    import PocketBase from 'pocketbase';
    import { onMount } from 'svelte';

    const pb = new PocketBase('http://125.236.152.120:80');
    let user;
    let posts = [];

    const getPosts = async () => {
        const result = pb.collection('posts').getList(1, 20, {
            sort: 'created',
        });
        posts = (await result).items;
    }

    const createPost = async () => {
        user = await pb.collection('users').getFirstListItem('email="test@example.com"').items;
        await pb.collection('posts').create({
            "title": "test title",
            "text": "this is some text that has been made up.",
        }).catch(error => console.log(user));
        await getPosts();
    };

    onMount(async () => {
        await getPosts();
    })

</script>

<h1>Hello, World!</h1>
<button on:click={createPost}>Create post</button>
<div class="posts">
    {#each posts as post (post.id)}
        <div class="post">
            <h1>{post.title}</h1>
            <p>{post.text}</p>
        </div>
    {/each}
</div>