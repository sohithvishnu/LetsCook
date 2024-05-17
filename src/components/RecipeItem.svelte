<script>
    import { db } from '../lib/firebase/firebase';

    export let index;
    export let recipe;
    export let editRecipe;
    export let removeRecipe;
    export let showActions = true;


    function confirmDelete() {
        if (confirm("Are you sure you want to delete this recipe?")) {
            removeRecipe(index);
        }
    }
</script>

<div class="recipe">
    <div class="recipe-details">
        <h3>{recipe.title}</h3>
        <p>Ingredients: {recipe.ingredients}</p>
        <p>Type of Food: {recipe.typeOfFood}</p>
        <p>Process: {recipe.process}</p>
    </div>
     <!-- Conditionally render actions based on the showActions prop -->
     {#if showActions}
     <div class="actions">
         <i
             on:click={() => {
                 editRecipe(index);
             }}
             class="fa-regular fa-pen-to-square"
             aria-label="Edit"
         />
         <i
             on:click={confirmDelete}
             class="fa-regular fa-trash-can"
             aria-label="Remove"
             style="cursor: pointer;"
         />
     </div>
     {/if}
</div>


<style>
    .recipe {
        border-left: 1px solid cyan;
        padding: 8px 14px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .recipe-details {
        flex: 1;
    }

    .actions {
        display: flex;
        align-items: center;
        gap: 14px;
        font-size: 1.3rem;
    }

    .actions i {
        cursor: pointer;
    }

    .actions i:hover {
        color: coral;
    }
    
    .space {
        width: 20px; /* Adjust the width as needed */
    }
</style>
