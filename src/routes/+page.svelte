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

    .header {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        z-index: 2;
        padding: 12px 20px;
        display: flex;
        flex-direction: column; /* Changed to column direction */
        justify-content: center;
        align-items: center;
        width: 100%;
        text-align: center; /* Center the items */
    }

    .logo {
        font-size: 248px; /* Increased font size for the logo */
        font-weight: bold;
        text-decoration: none;
        color: white;
        margin-bottom: 20px; /* Added margin bottom to separate logo from buttons */
    }

    .login-button,
    .Explore-button {
        display: inline-block;
        padding: 12px 30px; /* Increased padding for the buttons */
        font-size: 24px; /* Increased font size for the buttons */
        color: white;
        text-decoration: none;
        border: 2px solid white;
        border-radius: 30px; /* Increased border radius for the buttons */
        transition: background-color 0.3s, color 0.3s;
        margin: 10px; /* Increased margin to separate buttons */
    }

    .login-button:hover,
    .Explore-button:hover {
        background-color: rgb(255, 255, 255);
        color: rgb(40, 181, 136);
    }
</style>
<div class="mainImage"> 
    
    <div class="header">
        <a href="/" class="logo">LetsCook</a>
        {#if isLoggedIn}
            <a href="/dashboard" class="login-button">Dashboard</a>
        {:else}
            <a href="/authenticate" class="login-button">Login</a>
        {/if}
        <a href="/explore" class="Explore-button">Explore</a>
    </div>
    <img src={viewSpaceImage} alt="View Space" class="mainImage">
</div>
