<script>
    import { onMount } from 'svelte';
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    export let list_url = "http://localhost:5000/api/fonts/";
    export let label = "";
    export let selected = "";

    function changeSelected(value) {
		dispatch('change', {
			value: value
		});
	}
  
    // async data fetching function
    async function fetchData() {
        const response = await fetch(list_url);
        const json = await response.json();
        const items = json["data"];
        selected = items[0];
        changeSelected(selected);
        return items;
    }

    let promise = fetchData();

    function getFonts() {
        promise = fetchData();
    }

    onMount(async () => fetchData());
</script>

<style>
    label, select {
        display: inline;
        padding: 10px;
    }

    select {
        float: right;
        width: 59%;
        cursor: pointer;
        background: #ededed;
        box-shadow: 1px 2px 1px rgba(0, 0, 0, 0.5);
    }

    :focus, select:hover {
        outline: none;
        border: 1px solid rgba(61, 153, 112, 0.9);
    }  

    div {
        width: 100%;
        display: flex;
        justify-content: center;
        height: auto;
    }

        
    @media (max-width: 640px) {
        div {
            display: block;
        }

        select {
            float: right;
            width: 100%;
        }
    }

    label {
        width: 40%;
    }
</style>

{#await promise}
    <p>...waiting</p>
{:then items}
    <div>
        <label>{label}</label>
        <select bind:value={selected} on:change="{()=> changeSelected(selected)}">
            {#each items as item}
                <option value="{item}">
                    {item}
                </option>
            {/each}
        </select>
    </div>
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}

