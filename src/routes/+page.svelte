<script>
    import viewSpaceImage from "../public/img/mainImage.jpg"
    import { auth } from "../lib/firebase/firebase";

    const nonAuthRoutes = ["/", "/authenticate","/explore"];

    let isLoggedIn = false;

    auth.onAuthStateChanged(user => {
        isLoggedIn = !!user;
    });
</script>
<style>
    .mainImage {
        width: 100%; 
        height: auto; 
        max-width: 100%; 
    }

    .body {
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
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

    i {
        position: absolute;
        top: 27px;
       
        right: 160px;
        color: black;
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
</style>
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
    <img src={viewSpaceImage} alt="View Space" class="mainImage">
</div>
