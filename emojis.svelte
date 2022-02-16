<script>
import { onMount } from 'svelte';
import emojis from './emoji.js';
import EmojiContent from './emoji_content.svelte';

import { superstruct } from 'superstruct';

import { isLength, matches } from 'validator';

export let EmojiComp;
export let displayEmoji;

function CloseEmoji() {
    EmojiComp = { name:"", component:"" };
    displayEmoji = false;
}

let selected_emojis = [];
let selected_emoji = "Recent";
function Recent() {
    selected_emoji = "Recent";
    if(localStorage) {
        const emojis = localStorage.getItem("bolt_recent_emojis");
        if(emojis) selected_emojis = JSON.parse(emojis);
        else selected_emojis = [];
    }
}
function Load(place) {
    selected_emoji = place;
    if(place == "Recent") Recent();
    else selected_emojis = emojis.filter((emo)=>emo.category == place);
}

const search = superstruct({
    types:{
        search: value => {
            return  (matches(value, new RegExp('^[a-zA-Z0-9()&#@+-_*]{1,32}[a-zA-Z0-9()&#@+-_* ]{0,32}[a-zA-Z0-9()&#@+-_*]{0,32}$'))) && isLength(value, {min:1, max: 32});
        }
    }
});

const Search = search({
    search: 'search'
});

let search_data = {search: ""};
function SearchEmoji(event) {
    try {
        search_data = Search({search:event.target.value});
        const results = emojis.filter((emo) => {
            const regex = new RegExp(search_data.search, "gi");
            return emo.name.match(regex) || emo.key.match(regex);
        });
        if(results) selected_emojis = results;
        console.log(selected_emojis);
    } catch (message) {
        console.log(message);
        const { path, value } = message;
        if(value === ""){
            console.log(`🖖Your search is required!`);
        }
        else if(value.length > 32){
            console.log(`💥32 characters or less!`);
        }
        else{
            console.log(`💥Invalid Search!`);
        }
        search_data = false;
    }
}

onMount(()=>Recent());

</script>
<style>

section.main {
    display: flex;
    flex-flow: column nowrap;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    height: auto;
    padding: 5px;
}

section.sm_orb {
    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
    align-items: center;
    width: auto;
    height: auto;
    margin: 3px;
    padding: 5px;
    background:#151414;
    box-shadow: 0 0.1rem 0.001rem #0a0a0aa2, 0 -0.1rem 0.001rem #0a0a0aa2,
    0.1rem 0 0.001rem #0a0a0aa2, -0.1rem 0 0.001rem #0a0a0aa2;
    border-radius: 10px;
}

section.sm_orb .small {
    width: 16px;
    height: 16px;
}

section.search {
    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: auto;
    padding: 5px;
    background: #151414;
    box-shadow: 0 0.1rem 0.001rem #0a0a0aa2, 0 -0.1rem 0.001rem #0a0a0aa2,
    0.1rem 0 0.001rem #0a0a0aa2, -0.1rem 0 0.001rem #0a0a0aa2;
    border-radius: 15px;
}

section.search input {
    background: #151414;
    text-shadow: 0.08rem 0.17rem #131212;
    font-size: 1rem;
    border-radius: 15px;
    width: 100%;
    height: 30px;
    padding: 5px;
    border: none;
    outline: none;
    line-height: 1rem;
    cursor: text;
}

section.toolbar {
    display: flex;
    flex-flow: row nowrap;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    height: auto;
    padding: 5px;
}

section.content {
    display: flex;
    flex-flow: row wrap;
    justify-content: flex-start;
    align-items: flex-start;
    width: 300px;
    height: 160px;
    overflow: auto;
    padding: 5px;
}

section.space {
    width: 100%;
    margin: 5px;
    padding: 10px;
    height: auto;
}

</style>
<section class="main">
    <section class="search">
        <section class="sm_orb">
            <img src="/blue_search.svg" alt="Search Quilt" class="small">
        </section>
        <input on:input={SearchEmoji} type="search" placeholder="Search Emoji">
        <section on:click|stopPropagation={CloseEmoji} class="sm_orb cube">
            <img src="/red_cancel.svg" alt="Search Quilt" class="small">
        </section>
    </section>
    <section class="toolbar">
        <section on:click={()=>Load("Recent")} class="sm_orb">
            <img src="/{selected_emoji === "Recent" ? 'green' : 'blue'}_recent.svg" alt="Recent" class="small">
        </section>
        <section on:click={()=>Load("Smileys and People")} class="sm_orb">
            <img src="/{selected_emoji === "Smileys and People" ? 'green' : 'blue'}_smile.svg" alt="Smileys and People" class="small">
        </section>
        <section on:click={()=>Load("Animals and Nature")} class="sm_orb">
            <img src="/{selected_emoji === "Animals and Nature" ? 'green' : 'blue'}_paw.svg" alt="Animals and Nature" class="small">
        </section>
        <section on:click={()=>Load("Food and Drink")} class="sm_orb">
            <img src="/{selected_emoji === "Food and Drink" ? 'green' : 'blue'}_fruit.svg" alt="Food and Drink" class="small">
        </section>
        <section on:click={()=>Load("Activities")} class="sm_orb">
            <img src="/{selected_emoji === "Activities" ? 'green' : 'blue'}_football.svg" alt="Activities" class="small">
        </section>
        <section on:click={()=>Load("Travel and Places")} class="sm_orb">
            <img src="/{selected_emoji === "Travel and Places" ? 'green' : 'blue'}_hospital.svg" alt="Travel and Places" class="small">
        </section>
        <section on:click={()=>Load("Objects")} class="sm_orb">
            <img src="/{selected_emoji === "Objects" ? 'green' : 'blue'}_bulb.svg" alt="Objects" class="small">
        </section>
        <section on:click={()=>Load("Symbols")} class="sm_orb">
            <img src="/{selected_emoji === "Symbols" ? 'green' : 'blue'}_music.svg" alt="Symbols" class="small">
        </section>
        <section on:click={()=>Load("Flags")} class="sm_orb">
            <img src="/{selected_emoji === "Flags" ? 'green' : 'blue'}_flag.svg" alt="Flag" class="small">
        </section>
    </section>
    <section class="content">
        <EmojiContent bind:emojis={selected_emojis} on:onEmoji />
        <section class="space"></section>
    </section>
</section>