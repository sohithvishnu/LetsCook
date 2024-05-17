<script>
    import { onMount } from "svelte";
    import { auth, db } from "../lib/firebase/firebase";
    import { getDoc, doc, setDoc } from "firebase/firestore";
    import { authStore } from "../store/store";

    const nonAuthRoutes = ["/", "/authenticate","/explore"];

    onMount(async () => {
        console.log("Mounting");
        const unsubscribe = auth.onAuthStateChanged(async (user) => {
            const currentPath = window.location.pathname;

            if (!user && !nonAuthRoutes.includes(currentPath)) {
                window.location.href = "/authenticate";
                return;
            }

            if (user && nonAuthRoutes.includes(currentPath)) {
                window.location.href = "/dashboard";
                return;
            }

            if (!user) {
                authStore.update((curr) => ({ ...curr, loading: false }));
                return;
            }

            try {
                let userData;
                const docRef = doc(db, "users", user.uid);
                const docSnap = await getDoc(docRef);

                if (!docSnap.exists()) {
                    console.log("Creating User");
                    userData = {
                        email: user.email,
                        todos: [],
                    };
                    await setDoc(docRef, userData);
                } else {
                    console.log("Fetching User");
                    userData = docSnap.data();
                }

                authStore.update((curr) => ({
                    ...curr,
                    user,
                    data: userData,
                    loading: false,
                }));
            } catch (error) {
                console.error("Error fetching user data:", error);
                // Handle error, e.g., show an error message
            }
        });
        return unsubscribe;
    });
</script>


<div class="mainContainer">
    <slot />
</div>

<style>
    .mainContainer {
        min-height: 100vh;
        background: white;
        color: black;
        position: relative;
        display: flex;
        flex-direction: column;
    }


</style>
