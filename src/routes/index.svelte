<script context="module">
    import supabase from "$lib/db.js"; // TODO: Fix - $lib

    const printResume = () => {
        window.print();
    };
</script>

<script>
    // Database
    import { onMount } from "svelte";

    // Store
    import { writable } from "svelte/store";

    export const user = writable(supabase.auth.user() || false); // TODO: => "supabase"

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
            let { data, error } = await supabase // TODO: => "supabase"
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
            let { data, error } = await supabase // TODO: => "supabase"
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
            let { data, error } = await supabase // TODO: => "supabase"
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
            const { data, error } = await supabase // TODO: => "supabase"
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
            const { data, error } = await supabase // TODO: => "supabase"
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
            const { data, error } = await supabase // TODO: => "supabase"
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

    // create function that takes in startdate and enddate and calculates the duration
    const calculateDuration = (startDate, endDate) => {
        let start = new Date(startDate);
        let end = new Date(endDate);
        let duration = end.getTime() - start.getTime();
        let days = Math.floor(duration / (1000 * 60 * 60 * 24));
        let months = Math.floor(days / 30);
        let years = Math.floor(months / 12);
        // return in months unless month is greater than 12 then return in years and months
        if (years > 0) {
            return `${years} yrs - ${months % 12} mo`;
        } else if (months > 0) {
            return `${months} m`;
        } else {
            return `${days} days`;
        }
    };

    // create function that formats the date to be like 'May of 2022'
    const formatDate = (date) => {
        let newDate = new Date(date);
        let month = newDate.getMonth();
        let year = newDate.getFullYear();
        let months = [
            "January",
            "February",
            "March",
            "April",
            "May",
            "June",
            "July",
            "August",
            "September",
            "October",
            "November",
            "December",
        ];
        return `${months[month]}-${year}`;
    };
</script>

{#each myRecords as record}
    <div class="resume grid grid-cols-4 max-w-6xl mx-auto my-1 p-6">
        <div
            class=" col-span-4 text-left text-2xl md:text-xl uppercase font-extrabold"
        >
            {record.full_name}
        </div>
        <div class="col-span-4 text-left">
            <span class="flex">
                <a
                    target="_blank"
                    href="https://goo.gl/maps/ruwigxhhCrQcJMAX7"
                    class="flex"
                    >{record.address}
                    <img
                        src="/feather/map-pin.svg"
                        alt=""
                        class="ml-4 w-5 h-5"
                        id="location"
                    /></a
                >
            </span>
        </div>
        <div class="col-span-4 text-left">
            <a href="tel:{record.mobile}" class="flex">
                {record.mobile}
                <img
                    src="/feather/phone.svg"
                    alt=""
                    class="ml-4 w-5 h-5"
                    id="location"
                /></a
            >
        </div>
        <div class="col-span-4 text-left">
            <a href="mailto:{record.email}" class="flex"
                >{record.email}<img
                    src="/feather/mail.svg"
                    alt=""
                    class="ml-4 w-5 h-5"
                    id="location"
                /></a
            >
        </div>
        <div class="grid grid-flow-col gap-1 md:text-md">
            <a target="_blank" href={record.linkedin_url} class="w-12 ml-1">
                <img
                    src="/feather/linkedin.svg"
                    alt=""
                    class="w-12 h-12 p-2 bg-slate-200 mt-4 rounded-xl"
                />
            </a>
            <a target="_blank" href={record.facebook_url} class="w-12 ml-1">
                <img
                    src="/feather/facebook.svg"
                    alt=""
                    class="w-12 h-12 p-2 bg-slate-200 mt-4 rounded-xl"
                />
            </a>
            <button on:click={printResume} class="w-12 ml-1">
                <img
                    src="/feather/printer.svg"
                    alt=""
                    class="w-12 h-12 p-2 bg-slate-200 mt-4 rounded-xl"
                />
            </button>
        </div>

        <div
            class="bg-slate-200 p-2 rounded-lg col-span-4 text-left uppercase text-md md:text-sm mt-4 font-bold"
        >
            Summary
        </div>
        <div class="col-span-4 text-justify my-4">
            {record.summary}
        </div>
        <div
            class="bg-slate-200 p-2 rounded-lg  col-span-4 text-left uppercase text-md mt-4 font-bold md:text-md"
        >
            Experience
        </div>
        {#each jobs as job}
            <div class="my-1 col-span-4">
                <div
                    class="bg-slate-50 p-2 rounded-lg col-span-4 text-left text-sm my-1 uppercase italic font-bold"
                >
                    {job.company}
                </div>
                <div class="col-span-4 text-lg text-left font-semibold">
                    {job.position}
                </div>
                <div class="col-span-4 text-left font-semibold">
                    <span class="font-semibold"
                        >{formatDate(job.startdate)}</span
                    >
                    <span class="text-xs italic"
                        >({calculateDuration(job.startdate, job.enddate)})</span
                    >
                    <span>{formatDate(job.enddate)}</span>
                </div>

                <div class="col-span-4 text-justify text-sm my-1 font-semibold">
                    <p>{job.position_description}</p>
                </div>
                <div class="mx-auto max-w-2xl my-2">
                    <img
                        src={job.example_design_url}
                        alt="Sawmill"
                        class="mx-auto"
                    />
                </div>
                <div class="col-span-4 text-justify text-sm my-1">
                    <p>{job.job_duties}</p>
                </div>
                <div class="col-span-4 text-justify text-sm my-1">
                    <p>{job.skills ? job.skills : ""}</p>
                </div>
            </div>
        {/each}
    </div>
{/each}
<svelte:window on:keypress={handleKeyPress} />

<style>
</style>
