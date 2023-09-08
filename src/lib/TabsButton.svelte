<script>
    import { createEventDispatcher, onMount } from "svelte";

	export let tabs = []
    export let activeTab = ''
    export let color = {}

    const dispatch = createEventDispatcher();

	let activeTabPosition = {}

	const getPosition = (value)=>{
		if(tabs.length){
			let collectWidth = []
			const filterTabs = tabs.filter(f=>!f.hidden)

			filterTabs.forEach(item => {
				const elm = document.getElementById(item.value)
				if(elm){
					const {width} = elm.getBoundingClientRect()
					collectWidth.push(width)
				}
			});

			const foundIndex = filterTabs.findIndex(f=> f.value === value)

			let sum = 0
			collectWidth.slice(0, foundIndex).forEach( num => {
				sum += num;
			})

			activeTabPosition = {
				left: foundIndex === 0 ? 0 : sum, 
				width: collectWidth[foundIndex]
			}
		}
	}

    onMount(()=>{
        if(activeTab){
            setTimeout(()=>{
				getPosition(activeTab)
            }, 500)
        }
    })
</script>

<div 
    class="tabs-container"
    style:--bottom-line={color.bottomLine || '#999'}
    style:--active={color.active || '#ccc'}
    style:--hover={color.hover || '#333'}
>
	<div class="bottom-line"/>
	<div class="tabs">
		<div
			class="active"
			style="width: {activeTabPosition.width}px; left: {activeTabPosition.left}px; bottom: 0px;"
		/>
		{#each tabs as t}
            <div bind:this={t.parent}>
                <button
                    class="item"
                    id={t.value}
                    class:hover={activeTab !== t.value}
                    on:click={() => {
                        dispatch('click', t)
                        getPosition(t.value)
                    }}
                >
                    <p>{t.title}</p>
                </button>
            </div>
		{/each}
	</div>
	<div>
		<slot />
	</div>
</div>

<style>
    .tabs-container {
        position: relative;
        width: 100%;
        height: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    .bottom-line {
        position: absolute;
        width: 100%;
        height: 2px;
        bottom: 1px;
        background: var(--bottom-line);
    }
    .tabs {
        position: relative;
        display: flex;
        align-items: center;
        width: max-content;
        overflow-x: auto;
        height: 36px;
    }
    .item {
        padding: 0 16px;
        height: 30px;
        white-space: nowrap;
        flex: none;
    }
    .hover:hover {
        color: var(--hover);
    }
    .active {
        position: absolute;
        height: 4px;
        border-radius: 0.5rem;
        transition-property: all;
        transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
        transition-duration: 300ms;
        background: var(--active);
    }
</style>
