<script>
    const verbal = "Вербальный";
    const somatic = "Соматический";
    const material = "Материальный";
    export let level;
    export let concentration;
    export let ritual;
    export let school;
    export let components;
    export let name;
</script>
<style>
    :root {
        --red: #f00;
        --green: #0f0;
        --blue: #00f;
        --yellow: #ff0;
        --cyan: #0ff;
        --purple: #f0f;
        --white: #fff;
    }
    .grid-container {
        color: white;
        display: grid;
        background-color: rgb(50,50,50);
        border-radius: 10px;
        height: 80px;
        margin: 10px;
        grid-template-areas:
            'level name name name name favourites'
            'level concentration ritual school components favourites';
    }

    .name-ge {
        /*background: var(--red);*/
        grid-area: name;
    }
    .level-ge {
        /*background: var(--green);*/
        grid-area: level;
        font-size: 50px;
        text-align: center;
        height: 100%;
    }
    .concentration-ge {
        /*background: var(--blue);*/
        grid-area: concentration;
    }
    .ritual-ge {
        /*background: var(--yellow);*/
        grid-area: ritual;
    }
    .school-ge {
        /*background: var(--cyan);*/
        grid-area: school;
    }
    .components-ge {
        /*background: var(--purple);*/
        grid-area: components;
    }
    .favourites-ge {
        /*background: var(--white);*/
        grid-area: favourites;
    }
</style>

<div class="grid-container">
    <div class="name-ge">
        <div class="name">{name}</div>
    </div>
    <div class="level-ge">
        <div class="level">{level}</div>
    </div>
    <div class="concentration-ge">
        {#if concentration}
            <div class="concentration">К</div>
        {/if}
    </div>
    <div class="ritual-ge">
        {#if ritual}
            <div class="ritual">Р</div>
        {/if}
    </div>
    <div class="school-ge">
        <div class="school">{school}</div>
    </div>
    <div class="components-ge">
        <div class="components">{components.map(c => c[0])}</div>
    </div>
    <div class="favourites-ge">
        <div class="favourites">S</div>
    </div>
</div>
