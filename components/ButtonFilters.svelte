<script context="module">
    export const evidenceInclude = true;
</script>

<script>
    import { getInputContext } from '@evidence-dev/sdk/utils/svelte';

    const inputs = getInputContext();

    export let items = "";
    let itemsArray = items.split(",");
    export let selectedItems = itemsArray;
    let initialState = true;

    /** @type {string} */
    export let name;

    $inputs[name] = selectedItems.map(item => `'${item}'`).join(', ');

    export

    /** @param {MouseEvent} event */
	function buttonFiltersToggle(event) {
		let btn = document.getElementById(event.target.id);
        btn.classList.toggle("bg-blue-500");
        btn.classList.toggle("hover:bg-gray-50");
        btn.classList.toggle("hover:bg-blue-300");
        btn.classList.toggle("text-gray-700");
        btn.classList.toggle("hover:text-gray-700");
        btn.classList.toggle("text-white");
        if(initialState){
            selectedItems = [];
            initialState = false;
        }
        if(!selectedItems.includes(event.target.id)){
            selectedItems.push(event.target.id);
        } else {
            selectedItems.splice(selectedItems.indexOf(event.target.id),1);
        }
        if(selectedItems.length === 0){
            $inputs[name] = itemsArray.map(item => `'${item}'`).join(', ');
        }else{
            $inputs[name] = selectedItems.map(item => `'${item}'`).join(', ');
        }
	}
</script>
<div class="flex justify-center flex-wrap">
{#each itemsArray as item}
    <button id="{item}" on:click={buttonFiltersToggle} class="px-4 py-2 text-sm font-medium text-gray-700 hover:bg-gray-50 focus:relative">{item}</button>
{/each}
</div>
