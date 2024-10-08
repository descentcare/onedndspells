<script>
    import SpellLink from './SpellLink.svelte';
    import SpellCard from './SpellCard.svelte';
    import spells from './spells.json';
    spells.spells.sort((s1, s2) =>
        s1.level - s2.level || s1.name.localeCompare(s2.name));

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
            && !spell.nameEng.toLowerCase().includes(filter.text.toLowerCase())
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
            && Object.hasOwn(spell, "concentration") == filter.concentrationNotRequired
            || filter.isRitual != filter.isNotRitual
            && Object.hasOwn(spell, "ritual") == filter.isNotRitual
            || filter.consumesComponent != filter.doesntConsumesComponent
            && Object.hasOwn(spell, "consumesComponent") == filter.doesntConsumesComponent
        ;
        return !hideSpell;
    };
    let modalDisplay = false;
    function displayModal(isDisplayed) {
        modalDisplay = isDisplayed;
    }

    let spellToDisplay = null;
    function displaySpell(spell) {
        spellToDisplay = spell;
    }
    let outerWidth;
    $: onDesktop = outerWidth >= 768;
</script>

<style>
    main {
        color: white;
    }

    .hide {
        display: none;
    }

    .filter-modal-content .filterCheckboxesContainer {
        display: flex;
        width: 90%;
        flex-wrap: wrap;
        justify-content: flex-start;
        align-items: baseline;
    }

    .filterCheckboxesContainer input[type='checkbox'] {
        -webkit-appearance: none;
        appearance: none;
        background-color: #fff;
        margin: 0;
    }

    .filterCheckbox {
        display: block;
        border-radius: 4px;
        margin: 5px;
        padding: 5px;
        font-size: 20px;
        text-align: center;
    }

    .filterCheckbox {
        background-color: rgb(15,15,15);
    }

    .filterCheckbox:has(input[type='checkbox']:checked) {
        background-color: rgb(35,35,35);
    }

    .filter-modal {
        overscroll-behavior: none;
        display: block;
        position: fixed;
        z-index: 10;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: auto;
        background-color: rgb(0, 0, 0);
        background-color: rgba(0, 0, 0, 0.4);
    }

    .filter-modal-content {
        background-color: rgb(20,20,20);
        margin: 1% auto;
        padding: 20px;
        border: 1px solid #888;
        border-radius: 10px;
        width: 90%;
    }

    .close {
        padding: 20px;
        position: relative;
        top: 30px;
        color: #aaa;
        float: right;
        font-size: 50px;
        font-weight: bold;
    }

    .close:hover,
    .close:focus {
        color: black;
        text-decoration: none;
        cursor: pointer;
    }

    .searchbarContainer {
        z-index: 5;
        background-color: rgb(23,23,23);
        font-size: 20px;
        top: 0px;
        left: 0px;
        width: 100%;
        position: sticky;
        padding: 10px;
    }
    .searchbar {
        width: 80%;
        height: 30px;
    }

    .openFilterModal {
        height: 30px;
    }

    .flexContainerMain {
        display: flex;
        position: relative;
        flex-direction: row-reverse;
        justify-content: flex-end;
        background-color: rgb(23,23,23);
    }

    .spellCard {
        z-index: 2;
        top: 1px;
        display: block;
        position: fixed;
        overflow: auto;
        height: 100%;
        background-color: inherit;
    }

    .spellList {
        display: block;
        min-width: 30%;
        flex-grow: 1;

        display: flex;
        flex-direction: column;
        flex-wrap: nowrap;
        overflow: auto;
        height: 100%;
        position: relative;
    }

    .spellLink {
        width: 95vw;
    }
    @media only screen and (min-width: 768px) {
        .spellList {
            flex-wrap: wrap;
            flex-direction: row;
        }

        .spellLink {
            width: 30vw;
        }

        .filter-modal-content {
            width: 60%;
        }

        .spellCard {
            position: sticky;
            min-width: 70%;
            overflow: auto;
        }
    }
</style>

<svelte:window bind:outerWidth />
<main>
    <div class="searchbarContainer" class:hide={spellToDisplay != null && !onDesktop}>
        <input class="searchbar" bind:value={filter.text}/>
        <button class="openFilterModal" on:click={(e) => displayModal(true)}>Фильтр</button>
    </div>
    {#if modalDisplay}
        <div class="filter-modal" on:click|self={(e) => displayModal(false)}>
            <div class="filter-modal-content">
                <span class="close" on:click={(e) => displayModal(false)}>&times;</span>
                <p>Концентрация</p>
                <div class="filterCheckboxesContainer">
                    <label>
                        <div class="filterCheckbox">
                            <input type='checkbox' bind:checked={filter.concentrationRequired}/>
                            <span>Требуется</span>
                        </div>
                    </label>
                    <label>
                        <div class="filterCheckbox">
                            <input type='checkbox' bind:checked={filter.concentrationNotRequired}/>
                            <span>Не требуется</span>
                        </div>
                    </label>
                </div>
                <hr/>
                <p>Ритуал</p>
                <div class="filterCheckboxesContainer">
                    <label>
                        <div class='filterCheckbox'>
                            <input type='checkbox' bind:checked={filter.isRitual}/>
                            <span>Да</span>
                        </div>
                    </label>
                    <label>
                        <div class='filterCheckbox'>
                            <input type='checkbox' bind:checked={filter.isNotRitual}/>
                            <span>Нет</span>
                        </div>
                    </label>
                </div>
                <hr/>
                <p>Компонент</p>
                <div class="filterCheckboxesContainer">
                    <label>
                        <div class='filterCheckbox'>
                            <input type='checkbox' bind:checked={filter.onlySelectedComponents}/>
                            <span>Только выбранные компоненты</span>
                        </div>
                    </label>
                    {#each components as component, index}
                        <label>
                            <div class='filterCheckbox'>
                                <input type='checkbox' bind:checked={filter.components[index]}/>
                                <span>{component}</span>
                            </div>
                        </label>
                    {/each}
                    <label>
                        <div class='filterCheckbox'>
                            <input type='checkbox' bind:checked={filter.consumesComponent}/>
                            <span>Расходуемый</span>
                        </div>
                    </label>
                    <label>
                        <div class='filterCheckbox'>
                            <input type='checkbox' bind:checked={filter.doesntConsumesComponent}/>
                            <span>Не расходуемый</span>
                        </div>
                    </label>
                </div>
                <hr/>
                <p>Уровни</p>
                <div class="filterCheckboxesContainer">
                    {#each filter.levels as level, index}
                        <label>
                            <div class='filterCheckbox'>
                                <input type='checkbox' bind:checked={filter.levels[index]}/>
                                <span>{index}</span>
                            </div>
                        </label>
                    {/each}
                </div>
                <hr/>
                <p>Школы магии</p>
                <div class="filterCheckboxesContainer">
                    {#each filter.schools as school, index}
                        <label>
                            <div class='filterCheckbox'>
                                <input type='checkbox' bind:checked={filter.schools[index]}/>
                                <span>{schools[index]}</span>
                            </div>
                        </label>
                    {/each}
                </div>
                <hr/>
                <p>Классы</p>
                <div class="filterCheckboxesContainer">
                    {#each filter.classes as c, index}
                        <label>
                            <div class='filterCheckbox'>
                                <input type='checkbox' bind:checked={filter.classes[index]}/>
                                <span>{classes[index]}</span>
                            </div>
                        </label>
                    {/each}
                </div>
                <hr/>
                <p>Время накладывания</p>
                <div class="filterCheckboxesContainer">
                    {#each filter.castingTimes as c, index}
                        <label>
                            <div class='filterCheckbox'>
                                <input type='checkbox' bind:checked={filter.castingTimes[index]}/>
                                <span>{castingTimes[index]}</span>
                            </div>
                        </label>
                    {/each}
                </div>
                <hr/>
                <p>Время накладывания</p>
                <div class="filterCheckboxesContainer">
                    {#each filter.distances as d, index}
                        <label>
                            <div class='filterCheckbox'>
                                <input type='checkbox' bind:checked={filter.distances[index]}/>
                                <span>{distances[index]}</span>
                            </div>
                        </label>
                    {/each}
                </div>
                <hr/>
                <p>Длительность</p>
                <div class="filterCheckboxesContainer">
                    {#each filter.durations as d, index}
                        <label>
                            <div class='filterCheckbox'>
                                <input type='checkbox' bind:checked={filter.durations[index]}/>
                                <span>{durations[index]}</span>
                            </div>
                        </label>
                    {/each}
                </div>
            </div>
        </div>
    {/if}

    <div class="flexContainerMain">
        {#if spellToDisplay != null}
            <div class="spellCard">
                <span class="close" on:click={(e) => displaySpell(null)}>&times;</span>
                <SpellCard spell={spellToDisplay}/>
            </div>
        {/if}


        <div class="spellList" class:hide={spellToDisplay != null && !onDesktop}>
            {#each spells.spells as spell}
                {#if showSpell(spell, filter)}
                    <div class="spellLink" on:click={(e) => displaySpell(spell)}>
                        <SpellLink {...spell}/>
                    </div>
                {/if}
            {/each}
        </div>
    </div>
</main>
