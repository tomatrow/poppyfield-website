<script lang="ts">
    import { onMount } from "svelte"

    export let template: string
    export let plan: string
    export let action: string
    export let endpoint = "http://localhost:3000/api/create-checkout-session"

    function createDestinationUrl(base: string, params) {
        const url = new URL(base)
        url.search = new URLSearchParams(params).toString()
        return url.toString()
    }

    function addButtonListeners() {
        function updateCart({ target }: MouseEvent) {
            const button = target as HTMLElement
            template = button.dataset.template ?? template
            plan = button.dataset.plan ?? plan
        }
        const buttons: HTMLElement[] = Array.from(document.querySelectorAll(".cart-button"))
        buttons.forEach(button => button.addEventListener("click", updateCart))
        return () => buttons.forEach(button => button.removeEventListener("click", updateCart))
    }

    onMount(addButtonListeners)

    $: console.log({ template, plan })

    // https://stackoverflow.com/a/46428962
    $: checkoutLink = createDestinationUrl(endpoint, {
        plan,
        template
    })
</script>

<a href="#" class="cart-button" data-template="vasi">vasi</a>
<a href="#" class="cart-button" data-template="paparouna">paparouna</a>
<a href="#" class="cart-button" data-plan="essentials">essentials</a>
<a href="#" class="cart-button" data-plan="advanced">advanced</a>
<a href="#" class="cart-button" data-plan="premium">premium</a>

<a href="#" onClick="">link</a>

<footer>
    {#if checkoutLink}
        <a href={checkoutLink}>Purchase</a>
    {/if}
</footer>
