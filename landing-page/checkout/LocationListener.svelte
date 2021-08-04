<script lang="ts">
    import { onMount, createEventDispatcher } from "svelte"

    const dispatch = createEventDispatcher()

    export let location: Document["location"]

    onMount(() => {
        const observer = new MutationObserver(mutations => {
            mutations.forEach(mutation => {
                console.log(mutation)
                if (location?.href === document.location.href) return

                location = document.location
                dispatch("location", { location })
            })
        })

        observer.observe(document.body, {
            childList: true,
            subtree: true
        })

        return () => observer?.disconnect()
    })
</script>

<svelte:window on:popstate={() => console.log("change")} />
<slot {location} />
