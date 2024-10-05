<script>
    export let level;
    export let classes;
    export let duration;
    export let concentration;
    export let ritual;
    export let school;
    export let components;
    export let name;
</script>
<style>
    div.spellLink {
        background-color: lightblue;
        width: 30%;
        border-radius: 10px;
        height: 42px;
    }
</style>

<div class="spellLink">
    <div class="level">{level}</div>
    {#if concentration}
        <div class="concentration">К</div>
    {/if}
    {#if ritual}
        <div class="ritual">Р</div>
    {/if}
    <div class="school">{school}</div>
    <div class="components">{components}</div>
    <div class="name">{name}</div>
</div>
<br/>
