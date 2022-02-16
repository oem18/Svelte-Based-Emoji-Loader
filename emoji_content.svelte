<script>
import { createEventDispatcher } from 'svelte';
export let emojis = [];

const dispatch = createEventDispatcher();

function Selected(emoji) {
    if(localStorage) {
        let emos = [];
        const emojis = localStorage.getItem("bolt_recent_emojis");
        if(emojis) emos = JSON.parse(emojis);
        const One = emos.findIndex((emo)=>{
            return emo.emoji == emoji.emoji;
        });
        if(One != -1 && One != null) {
            emos.splice(One, 1);
            emos = emos;
            emos = [emoji, ...emos];
        } else emos = [emoji, ...emos];
        localStorage.setItem("bolt_recent_emojis", JSON.stringify(emos));
    }
    dispatch("onEmoji", emoji);
}

</script>
<style>
    
    section.sm_orb {
        display: flex;
        flex-flow: row nowrap;
        justify-content: center;
        align-items: center;
        width: auto;
        height: auto;
        margin: 3px;
        padding: 5px;
        background: #151414;
        box-shadow: 0 0.1rem 0.001rem #0a0a0aa2, 0 -0.1rem 0.001rem #0a0a0aa2,
        0.1rem 0 0.001rem #0a0a0aa2, -0.1rem 0 0.001rem #0a0a0aa2;
        border-radius: 10px;
    }

    section.sm_orb p {
        display: flex;
        flex-flow: row nowrap;
        justify-content: center;
        align-items: center;
        width: 16px;
        height: 16px;
        cursor: default;
    }

</style>
{#each emojis as emoji }
    <section on:click={()=>Selected(emoji)} class="sm_orb">
        <p>
            {emoji.emoji}
        </p>
    </section>
{/each}