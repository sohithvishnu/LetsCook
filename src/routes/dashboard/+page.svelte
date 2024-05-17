<script>
    import { db } from "../../lib/firebase/firebase";
    import { authHandlers, authStore } from "../../store/store";
    import { getDoc, doc, setDoc, collection, getDocs, updateDoc, deleteDoc} from "firebase/firestore";
    import RecipeItem from "../../components/RecipeItem.svelte"; // Assuming you have a component for displaying recipe items

    let recipeList = [];
    let newRecipe = {
        title: "",
        ingredients: "",
        typeOfFood: "",
        process: "",
        isPublic: false // Add a field to track if the recipe is public
    };
    let error = false;
    let showInputFields = false; // Add a variable to control the visibility of input fields

    authStore.subscribe((curr) => {
        recipeList = curr.data.recipes || [];
    });

    function addRecipe() {
        error = false;
        if (!newRecipe.title || !newRecipe.ingredients || !newRecipe.typeOfFood || !newRecipe.process) {
            error = true;
            return;
        }
        recipeList = [...recipeList, newRecipe];
        newRecipe = {
            title: "",
            ingredients: "",
            typeOfFood: "",
            process: "",
            isPublic: false
        };
        showInputFields = false; // Reset to hide the input fields after adding the recipe
    }

    function toggleInputFields() {
        if (showInputFields) {
            addRecipe(); // Add the edited recipe
            showInputFields = !showInputFields; 
            showInputFields = false; 
        }
        else {
            showInputFields = !showInputFields; // Toggle the visibility of input fields
        }
       
    }

    function editRecipe(index) {
        let editedRecipe = recipeList[index];
        newRecipe = editedRecipe;
        recipeList = recipeList.filter((recipe, i) => i !== index);
        showInputFields = true;
    }

    async function removeRecipe(index) {
        recipeList = recipeList.filter((recipe, i) => i !== index);
        try {
        const removedRecipe = recipeList[index];

        // Remove from the user's recipes
        recipeList = recipeList.filter((recipe, i) => i !== index);

        // Remove from the public database if it was marked as public
        if (removedRecipe.isPublic) {
            const publicRecipeRef = doc(db, "public_recipes", removedRecipe.title);
            await deleteDoc(publicRecipeRef);
        }
    } catch (error) {
        console.error("Error removing recipe:", error);
    }
    }

    async function saveRecipes() {
        try {
            const userRef = doc(db, "users", $authStore.user.uid);
            await setDoc(
                userRef,
                {
                    recipes: recipeList,
                },
                { merge: true }
            );

            // Make public recipes available to the Explore page
            const publicRecipes = recipeList.filter(recipe => recipe.isPublic);
            for (const recipe of publicRecipes) {
                const publicRecipeRef = doc(db, "public_recipes", recipe.title);
                await setDoc(publicRecipeRef, recipe, { merge: true });
            }
        } catch (err) {
            console.log("There was an error saving your information");
        }
    }


</script>

{#if !$authStore.loading}
    <div class="mainContainer">
        <div class="headerContainer">
            <div class="leftContent">
                <a href="/" class="logo">Lets Cook</a>
                <div class="button-group">
                    <a href="/" class="Home-button">Home</a>
                    <a href="/explore" class="Explore-button">Explore</a>
                </div>
            </div>
            <div class="headerBtns">
                <button on:click={saveRecipes}>
                    <p>Save</p>
                </button>
                <button on:click={authHandlers.logout}>
                    <p>Logout</p>
                </button>
            </div>
        </div>
        <main>
            <h1>Your Recipes</h1>
            {#if recipeList.length === 0}
                <p>You have no recipes yet!</p>
            {/if}
            {#each recipeList as recipe, index}
                <RecipeItem {recipe} {index} {removeRecipe} {editRecipe} />
            {/each}
        </main>
        <div class="addRecipeContainer">
            {#if showInputFields}
                <div class="inputContainer">
                    <div class="inputRow">
                        <input bind:value={newRecipe.title} type="text" placeholder="Recipe Title" />
                        <input bind:value={newRecipe.typeOfFood} type="text" placeholder="Type of Food" />
                    </div>
                    <div class="inputRow">
                        <input bind:value={newRecipe.ingredients} type="text" placeholder="Ingredients" />
                    </div>
                    <div class="inputRow">
                        <textarea bind:value={newRecipe.process} placeholder="Process"></textarea>
                    </div>
                    <div class="inputRow">
                        <label>
                            <input type="checkbox" bind:checked={newRecipe.isPublic} />
                            Make Public
                        </label>
                    </div>
                    <div class="buttonGroup">
                        <button on:click={addRecipe}>Add Recipe</button>
                        <button on:click={toggleInputFields}>Cancel</button>
                    </div>
                </div>
            {:else}
                <button on:click={toggleInputFields}>Add Recipe</button>
            {/if}
        </div>
    </div>
{/if}

<style>
    /* Your existing CSS styles */
    .mainContainer {
        display: flex;
        flex-direction: column;
        gap: 24px;
        padding: 24px;
        width: 100%;
        max-width: 1000px;
        margin: 60px auto 0;
        align-items: center;
    }

    .headerContainer {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        z-index: 2; 
        background-color: rgba(255, 255, 255, 0.8);
        padding: 12px 20px;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .headerContainer .leftContent {
        display: flex;
        align-items: center;
    }

    .headerContainer .leftContent .button-group {
        margin-left: 20px;
    }

    .headerContainer .leftContent .button-group a {
        margin-left: 10px;
    }

    main {
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;
        width: 100%;
        max-width: 800px;
    }

    .logo {
        font-size: 24px;
        font-weight: bold;
        text-decoration: none;
        color: rgb(0, 0, 0);
    }

    .button-group {
        display: flex;
        align-items: center;
    }

    .button-group a {
        display: inline-block;
        padding: 8px 20px;
        color: black;
        text-decoration: none;
        border: 2px solid black;
        border-radius: 20px;
        transition: background-color 0.3s, color 0.3s;
        margin-left: 10px;
    }

    .button-group a:hover {
        background-color: black;
        color: white;
    }

    .headerBtns {
        display: flex;
        align-items: center;
        gap: 14px;
    }

    .headerContainer button {
        background: #003c5b;
        color: white;
        padding: 10px 18px;
        border: none;
        border-radius: 4px;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 10px;
        cursor: pointer;
    }

    .headerContainer button:hover {
        opacity: 0.7;
    }

    /* Additional styles for adding recipes */
    .addRecipeContainer {
        position: fixed;
        bottom: 20px;
        left: 50%;
        transform: translateX(-50%);
        display: flex;
        flex-direction: column;
        align-items: stretch;
        border: 1px solid #0891b2;
        border-radius: 5px;
        overflow: hidden;
        width: 100%;
        max-width: 80%;
        padding: 10px;
        background-color: #f9f9f9;
    }

    .inputContainer {
        position: relative;
    }

    .inputRow {
        display: flex;
        gap: 10px;
    }

    .inputRow input,
    .inputRow textarea {
        flex: 1;
    }

    .buttonGroup {
        display: flex;
        justify-content: space-between;
        margin-top: 10px;
    }

    .buttonGroup button {
        padding: 10px 0;
        flex: 1;
        margin-right: 5px;
    }

     .addRecipeContainer input,
    .addRecipeContainer textarea {
        margin-bottom: 10px;
        padding: 8px;
        border: 1px solid #ccc;
        border-radius: 4px;
    }

    .addRecipeContainer textarea {
        resize: vertical; /* Allow vertical resizing of textarea */
    }


    .addRecipeContainer button {
        padding: 10px 0;
        background: #003c5b;
        border: none;
        color: white;
        font-weight: 600;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    .addRecipeContainer button:hover {
        background-color: #001f2e;
    }
    .addRecipeContainer textarea {
        resize: vertical; /* Allow vertical resizing of textarea */
    }
</style>
