<script>
    import SpellLink from './SpellLink.svelte';
    import spells from './spells.json';
    const components = ["Вербальный", "Соматический", "Материальный"];
    const schools = ["Воплощение", "Вызов", "Иллюзия", "Некромантия",
        "Ограждение", "Очарование", "Преобразование", "Прорицание"];
    const classes = ["Бард", "Жрец", "Следопыт", "Друид", "Колдун",
        "Паладин", "Чародей", "Волшебник"];
    const castingTimes = ["Действие", "1 минута", "реакция",
        "1 час", "10 минут", "12 часов", "24 часа",
        "8 часов", "Бонусное действие" ];
    const distances = [
        "На себя", "Касание", "1 миля", "5 футов", "10 футов",
        "15 футов", "30 футов", "60 футов", "90 футов", "100 футов",
        "120 футов", "150 футов", "300 футов", "500 футов",
        "500 миль", "В пределах видимости", "Неограниченная",
        "Особая"
    ];
    const durations = [ "Мгновенная", "1 раунд", "6 раундов",
        "1 минута", "10 минут", "1 час", "2 часа", "8 часов",
        "24 часа", "7 дней", "10 дней", "30 дней", "Особая",
        "До рассеивания или активации", "До рассеивания"
    ];
    let filter = 
    {
        text: "",
        levels: Array(10).map(() => false),
        schools: schools.map(() => false),
        onlySelectedComponents: false,
        components: components.map(() => false),
        consumesComponent: false,
        classes: classes.map(() => false),
        castingTimes: castingTimes.map(() => false),
        distances: distances.map(() => false),
        durations: durations.map(() => false),
        doesntConsumesComponent: false,
        concentrationRequired: false,
        concentrationNotRequired: false,
        isRitual: false,
        isNotRitual: false,
    };
    $: selectedComponents = components
        .filter((_, i) => filter.components[i]);
    $: selectedSchools = schools
        .filter((_, i) => filter.schools[i]);
    $: selectedClasses = classes
        .filter((_, i) => filter.classes[i]);
    $: selectedCastingTimes = castingTimes
        .filter((_, i) => filter.castingTimes[i]);
    $: selectedDistances = distances
        .filter((_, i) => filter.distances[i]);
    $: selectedDurations = durations
        .filter((_, i) => filter.durations[i]);

    function showSpell(spell, filter) {
        let wrongComponents = false;
        if (selectedComponents.length > 0 && selectedComponents.length < 3)
        {
            if (filter.onlySelectedComponents)
            {
                wrongComponents = !spell.components.every(c => selectedComponents.includes(c));
            }
            else
            {
                wrongComponents = !spell.components.some(c => selectedComponents.includes(c));
            }
        }

        let hideSpell = wrongComponents
            || filter.text.length > 0
                && !spell.name.toLowerCase().includes(filter.text.toLowerCase())
            || filter.levels.some(l => l)
                && !filter.levels[spell.level]
            || filter.schools.some(s => s)
                && !selectedSchools.includes(spell.school)
            || filter.classes.some(c => c)
                && !spell.classes.some(c => selectedClasses.includes(c))
            || filter.castingTimes.some(c => c)
                && !spell.castingTimes.some(c => selectedCastingTimes.includes(c))
            || filter.distances.some(d => d)
                && !selectedDistances.includes(spell.distance)
            || filter.durations.some(d => d)
                && !selectedDurations.includes(spell.duration)
            || filter.concentrationRequired != filter.concentrationNotRequired
                && spell.concentration == filter.concentrationNotRequired
            || filter.isRitual != filter.isNotRitual
                && spell.ritual == filter.isNotRitual
            || filter.consumesComponent != filter.doesntConsumesComponent
                && spell.consumesComponent == filter.doesntConsumesComponent
        ;
        return !hideSpell;
    };
</script>

<input bind:value={filter.text}/>
<br/>
<p>Концентрация</p>
<label>
    <input type='checkbox' bind:checked={filter.concentrationRequired}/>
    Требуется
</label>
<label>
    <input type='checkbox' bind:checked={filter.concentrationNotRequired}/>
    Не требуется
</label>

<p>Ритуал</p>
<label>
    <input type='checkbox' bind:checked={filter.isRitual}/>
    Да
</label>
<label>
    <input type='checkbox' bind:checked={filter.isNotRitual}/>
    Нет
</label>

<p>Компонент</p>
<label>
    <input type='checkbox' bind:checked={filter.onlySelectedComponents}/>
    Только выбранные компоненты
</label>
{#each components as component, index}
    <input type='checkbox' bind:checked={filter.components[index]}/>
    {component}
{/each}
<label>
    <input type='checkbox' bind:checked={filter.consumesComponent}/>
    Расходуемый
</label>
<label>
    <input type='checkbox' bind:checked={filter.doesntConsumesComponent}/>
    Не расходуемый
</label>
<p>Уровни</p>
{#each filter.levels as level, index}
<label>
    <input type='checkbox' bind:checked={filter.levels[index]}/>
    {index}
</label>
{/each}
<p>Школы магии</p>
{#each filter.schools as school, index}
<label>
    <input type='checkbox' bind:checked={filter.schools[index]}/>
    {schools[index]}
</label>
{/each}
<p>Классы</p>
{#each filter.classes as c, index}
<label>
    <input type='checkbox' bind:checked={filter.classes[index]}/>
    {classes[index]}
</label>
{/each}
<p>Время накладывания</p>
{#each filter.castingTimes as c, index}
<label>
    <input type='checkbox' bind:checked={filter.castingTimes[index]}/>
    {castingTimes[index]}
</label>
{/each}
<p>Время накладывания</p>
{#each filter.distances as d, index}
<label>
    <input type='checkbox' bind:checked={filter.distances[index]}/>
    {distances[index]}
</label>
{/each}
<p>Длительность</p>
{#each filter.durations as d, index}
<label>
    <input type='checkbox' bind:checked={filter.durations[index]}/>
    {durations[index]}
</label>
{/each}
<br/>

{#each spells.spells as spell}
    {#if showSpell(spell, filter)}
        <SpellLink {...spell}/>
    {/if}
{/each}
