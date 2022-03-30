<script>
    import { supabase, supabaseService } from "$lib/db.js"; // TODO: Fix - lib

    const anon = supabase;
    const admin = supabaseService;

    // Database
    // TODO: remove "supabaseService" import
    import { onMount } from "svelte";
    import { src_url_equal } from "svelte/internal";

    // Store
    import { writable } from "svelte/store";

    export const user = writable(admin.auth.user() || false); // TODO: => "supabase"

    // Variables
    let dbName = "resume-education";
    let record = "";
    export let myRecords = [];
    let select = "*";
    let update = [{}];
    // @ts-ignore
    let insert = [{ user_id: user.id }]; // TODO: user.id

    onMount(() => {
        getAllRecords();
    });
    const getAllRecords = async () => {
        try {
            let { data, error } = await admin // TODO: => "supabase"
                .from(dbName)
                .select("*")
                .order("created_at", { ascending: false });
            myRecords = data;
            console.log(
                "Records:",
                myRecords,
                "Number of Records:",
                myRecords.length
            );
        } catch (error) {
            console.log(error);
        }
    };
    const addNewRecord = async () => {
        try {
            const { data, error } = await admin // TODO: => "supabase"
                .from(dbName)
                .insert(insert);
            await getAllRecords();
            record = "";
        } catch (error) {
            console.log(error);
        }
    };
    const updateRecord = async (record) => {
        try {
            const { data, error } = await admin // TODO: => "supabase"
                .from(dbName)
                .update(update)
                .eq("id", record.id);
            await getAllRecords();
        } catch (error) {
            console.log(error);
        }
    };
    const deleteRecord = async (record) => {
        try {
            const { data, error } = await admin // TODO: => "supabase"
                .from(dbName)
                .delete()
                .eq("id", record.id);
            await getAllRecords();
        } catch (error) {
            console.log(error);
        }
    };
    const handleKeyPress = (event) => {
        if (event.key === "Enter" && record !== "") {
            addNewRecord();
        }
    };
    // @ts-ignore
    $: console.log(user.id);
</script>

<div class="text-center">
    <span>Title</span>
    <span class="text-xl">Total: {myRecords.length}</span>
</div>

<div class="add-todo mt-12">
    <input
        class="input input-bordered w-72 text-lg"
        type="text"
        bind:value={record}
    />
    <button class="btn btn-primary ml-1" on:click={() => addNewRecord()}
        >Add</button
    >
</div>

{#each myRecords as record}
    {JSON.stringify(record, null, 2)}
{:else}
    <p>No records found</p>
{/each}

<svelte:window on:keypress={handleKeyPress} />

<style>
</style>
