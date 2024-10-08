<script>
    export let spell;
</script>
<style>
    * {
        font-size: 20px;
    }
</style>
<div class="card">
    <p>{spell.name} <span>[{spell.nameEng}]</span> </p>
    <p>{spell.level}</p>
    <p>{spell.school}</p>
    <p>{spell.school}</p>
    <p>{spell.castingTimeDescription}</p>
    <p>{spell.distance}</p>
    <p>
        {spell.components.join(', ')}
        {#if Object.hasOwn(spell, "materialComponents")}
            ({spell.materialComponents})
        {/if}
    </p>
    <p>{spell.durationDescription}</p>
    <div>{@html spell.description.join('<br/>')}</div>
    <p>{spell.classes.join(', ')}</p>
</div>
