<script>
    import { db } from "../../lib/firebase/firebase";
    import { collection, getDocs } from "firebase/firestore";
    import { auth } from "../../lib/firebase/firebase";

    const nonAuthRoutes = ["/", "/authenticate", "/explore"];

    let isLoggedIn = false;
    let loading = true;

    auth.onAuthStateChanged(user => {
        isLoggedIn = !!user;
    });

    let publicRecipes = [];

    async function fetchPublicRecipes() {
        try {
            const querySnapshot = await getDocs(collection(db, "public_recipes"));
            publicRecipes = querySnapshot.docs.map(doc => doc.data());
            loading = false; // Set loading to false when data is fetched
        } catch (error) {
            console.error("Error fetching public recipes:", error);
        }
    }

    fetchPublicRecipes();

    function toggleDetails(index) {
        publicRecipes[index].showDetails = !publicRecipes[index].showDetails;
    }
</script>

<div class="mainImage">
    <div class="header">
        <a href="/" class="logo">Lets Cook</a>
        <div class="button-group">
            <a href="/" class="Home-button">Home</a>
            <a href="/explore" class="Explore-button">Explore</a>
        </div>
        <div class="search-bar-container">
            <input type="text" class="search-bar" placeholder="Search">
        </div>
        {#if isLoggedIn}
        <a href="/dashboard" class="login-button">Dashboard</a>
        {:else}
        <a href="/authenticate" class="login-button">Login</a>
        {/if}
    </div>
</div>

{#if loading}
<div class="loading">Loading...</div>
{:else}
<div class="mainContainer">
    <h1>Explore Public Recipes</h1>
    <div class="recipeGrid">
        {#each publicRecipes as recipe, index}
        <div class="recipeCard" class:selected={recipe.showDetails}>
            <h2>{recipe.title}</h2>
            <p>Type: {recipe.typeOfFood}</p>
            {#if recipe.showDetails}
                <p>Ingredients: {recipe.ingredients}</p>
                <p>Process: {recipe.process}</p>
            {/if}
            <button class="detailsButton" on:click={() => toggleDetails(index)}>{recipe.showDetails ? 'Hide Details' : 'More Information'}</button>
        </div>
        {/each}
    </div>
</div>
{/if}

{#if publicRecipes.length === 0 && !loading}
<p>No public recipes available yet.</p>
{/if}

<style>

    .mainContainer h1 {
        padding: 30px;
    }
    .loading {
        text-align: center;
        margin-top: 50px;
        font-size: 20px;
    }

    .header {
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        z-index: 2; 
        background-color: rgba(255, 255, 255, 0.8); /* Add background color to improve readability */
        padding: 12px 20px;
        display: flex;
        justify-content: space-between;
        align-items: center;
        width: 100%; /* Adjust as needed */
    }

    .logo {
        font-size: 24px;
        font-weight: bold;
        text-decoration: none;
        color: rgb(0, 0, 0);
    }

    .search-bar-container {
        position: flex;
        flex: 0.9;
    }

    .search-bar {
        background: transparent;
        border: 2px solid black;
        padding: 14px;
        color: black;
        width: 100%;
        border-radius: 50px;
        overflow: hidden;
    }

    input:focus {
        outline: none;
    }

    .login-button {
        display: inline-block;
        padding: 8px 20px;
        color: black;
        text-decoration: none;
        border: 2px solid black;
        border-radius: 20px;
        transition: background-color 0.3s, color 0.3s;
    }

    .login-button:hover {
        background-color: black;
        color: white;
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

    .mainContainer {
        position: relative;
        padding-top: 80px; /* Adjust according to the height of the header */
    }

    .recipeGrid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 24px;
        padding: 30px;
    }

    .recipeCard {
        background-color: #f9f9f9;
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        transition: box-shadow 0.3s;
    }

    .recipeCard h2 {
        margin-top: 0;
        font-size: 20px;
        font-weight: bold;
    }

    .recipeCard p {
        margin-bottom: 10px;
    }

    .detailsButton {
        background-color: #007bff;
        color: #fff;
        border: none;
        border-radius: 5px;
        padding: 8px 16px;
        font-size: 16px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    .detailsButton:hover {
        background-color: #0056b3;
    }

    .recipeCard[selected=true] {
        box-shadow: 0 0 16px rgba(0, 0, 0, 0.3);
    }
</style>
