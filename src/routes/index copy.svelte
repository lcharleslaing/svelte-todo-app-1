<script>
    import { supabase, supabaseService } from "$lib/db.js"; // TODO: Fix - $lib

    const anon = supabase;
    const admin = supabaseService;

    // Database
    // TODO: remove "supabaseService" import
    import { onMount } from "svelte";
    import { src_url_equal } from "svelte/internal";

    // Store
    import { writable } from "svelte/store";

    export const user = writable(admin.auth.user() || false); // TODO: => "supabase"

    let skills = [];
    // Variables
    let dbName = "resume-contact-info";
    let record = "";
    export let myRecords = [];
    let select = "*";
    let update = [{}];
    // @ts-ignore
    let insert = [{ user_id: $user.id }]; // TODO: $user.id

    onMount(() => {
        getAllRecords();
        getAllExperiences();
        getAllEducation();
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

    export let jobs = [];
    const getAllExperiences = async () => {
        try {
            let { data, error } = await admin // TODO: => "supabase"
                .from("resume-experience")
                .select("*")
                .order("startdate", { ascending: false });
            jobs = data;
            console.log("Jobs:", jobs, "Number of Records:", jobs.length);
        } catch (error) {
            console.log(error);
        }
    };

    export let schools = [];
    const getAllEducation = async () => {
        try {
            let { data, error } = await admin // TODO: => "supabase"
                .from("resume-education")
                .select("*")
                .order("graduation", { ascending: false });
            schools = data;
            console.log(
                "Schools:",
                schools,
                "Number of Records:",
                schools.length
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
    const updateTodo = async (record) => {
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
    const deleteTodo = async (record) => {
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

<!-- <div class="text-center">
    <span>Title</span>
    <span class="text-xl">Total: {myRecords.length}</span>
</div> -->

<!-- <div class="add-todo mt-12">
    <input
        class="input input-bordered w-72 text-lg"
        type="text"
        bind:value={record}
    />
    <button class="btn btn-primary ml-1" on:click={() => addNewRecord()}
        >Add</button
    >
</div> -->
<div class="container">
    {#each myRecords as record (record.id)}
        <!-- {JSON.stringify(record, null, 2)} -->
        <h1 class="text-center">{record.full_name}</h1>
        <div class="">
            <div class="text-center">{record.address}</div>
            <div class="text-center">
                <div class="text-center">{record.email}</div>
                <div class="text-center">{record.mobile}</div>
            </div>
        </div>
    {/each}
    <h2 class="text-center">Experience</h2>
    {#each jobs as job}
        <div class="text-center">{job.company}</div>
        <div class="text-center">{job.position}</div>
        <div class="text-center">{job.startdate}</div>
        <div class="text-center">{job.enddate}</div>
        <div class="text-center">{job.position_description}</div>
        <div class="text-center">{job.job_duties}</div>
        <div class="text-center">{job.skills}</div>
    {/each}
    <h2 class="text-center">Education</h2>
    {#each schools as item}
        <div class="text-center">{item.school}</div>
        <div class="text-center">{item.major_study}</div>
        <div class="text-center">{item.graduation}</div>
    {/each}
</div>
<svelte:window on:keypress={handleKeyPress} />

<style>
    :root {
        --letter-width: 8.5in;
        --letter-height: calc(var(--letter-width) * 1.29);
        --mobile-width: 93vw;
        --mobile-height: calc(var(--mobile-width) * 1.29);
    }

    .container {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr;
        grid-template-areas:
            "header header header"
            "content content content";
        grid-gap: 1rem;
        width: 100%;
        height: 100%;
        padding: 1rem;
        background-color: #fafafa;
    }
    .text-center {
        text-align: center;
    }

    .divider {
        border-top: 1px solid;
    }

    .float-left {
        float: left;
    }

    .float-right {
        float: right;
    }

    /* media queries */
    @media print {
        .container {
            width: var(--letter-width);
            height: var(--letter-height);
        }
    }

    @media screen and (min-width: 768px) {
        .container {
            width: var(--mobile-width);
            height: var(--mobile-height);
        }
    }

    @media screen and (min-width: 1024px) {
        .container {
            width: var(--letter-width);
            height: var(--letter-height);
        }
    }

    @media screen and (min-width: 1280px) {
        .container {
            width: var(--letter-width);
            height: var(--letter-height);
        }
    }

    @media screen and (min-width: 1440px) {
        .container {
            width: var(--letter-width);
            height: var(--letter-height);
        }
    }

    @media screen and (min-width: 1680px) {
        .container {
            width: var(--letter-width);
            height: var(--letter-height);
        }
    }

    @media screen and (min-width: 1920px) {
        .container {
            width: var(--letter-width);
            height: var(--letter-height);
        }
    }
</style>
