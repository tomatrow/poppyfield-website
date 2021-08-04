<script>
    import { onMount } from "svelte"
    class CartManager {
        constructor(plan, template) {
            this.plan = plan
            this.template = template
            this._addButtonListeners()
        }

        set template(newTemplate) {
            if (!newTemplate || this._template === newTemplate) return
            this._template = newTemplate
            this._update()
        }
        set plan(newPlan) {
            if (!newPlan || this._plan === newPlan) return
            this._plan = newPlan
            this._update()
        }

        _update() {
            try {
                this._updateCheckoutLink()
                this._updateCheckoutSelections()
            } catch (error) {
                console.error(error)
                this._queryCheckoutButtons().forEach(button => button.classList.remove("active"))
            }
        }

        _updateCheckoutSelections() {
            const selectors = Array.from(document.querySelectorAll(".checkout-selection"))
            console.log({ selectors })
            selectors.forEach((/** @type {HTMLAnchorElement} */ selector) => {
                if (selector.dataset.plan === "true" && this._plan) {
                    selector.innerText =
                        document.querySelector(`.cart-label[data-plan=${this._plan}]`)?.innerText ??
                        this._plan
                    selector.href = `#plan-${this._plan}`
                } else if (selector.dataset.template === "true" && this._template) {
                    selector.innerText =
                        document.querySelector(`.cart-label[data-template=${this._template}]`)
                            ?.innerText ?? this._template
                    selector.href = `#template-${this._template}`
                }
            })
        }

        _queryCheckoutButtons() {
            return Array.from(document.querySelectorAll(".checkout-button"))
        }

        async _updateCheckoutLink() {
            if (!(this._plan && this._template)) return

            const createCheckoutLinkEndpoint = this._createDestinationUrl(
                "https://poppyfield-dashboard.netlify.app/api/create-checkout-session.json",
                {
                    plan: this._plan,
                    template: this._template
                }
            )
            console.log({ createCheckoutLinkEndpoint })
            const response = await fetch(createCheckoutLinkEndpoint, {
                method: "get"
            })
            const { url } = await response.json()
            console.log({ url })

            this._queryCheckoutButtons().forEach(button => {
                button.href = url
                button.classList.add("active")
            })
        }

        _addButtonListeners() {
            const updateCart = ({ target }) => {
                const button = target
                console.log({ button })
                this.template = button.dataset.template
                this.plan = button.dataset.plan
                console.log({ plan: this._plan, template: this._template })
            }
            const buttons = Array.from(document.querySelectorAll(".cart-button"))
            buttons.forEach(button => button.addEventListener("click", updateCart))
        }

        _createDestinationUrl(base, params) {
            const url = new URL(base)
            url.search = new URLSearchParams(params).toString()
            return url.toString()
        }
    }

    onMount(() => {
        const params = new URLSearchParams(window.location.search)
        window.cartManager = new CartManager(params.get("plan"), params.get("template"))
    })
</script>

<h1 id="template-vasi" class="cart-label" data-template="vasi">The Vási Template</h1>
<h1 id="template-paparouna" class="cart-label" data-template="paparouna">The Paparoúna Template</h1>

<h1 id="plan-essentials" class="cart-label" data-plan="essentials">Essentials</h1>
<h1 id="plan-advanced" class="cart-label" data-plan="advanced">Advanced</h1>
<h1 id="plan-premium" class="cart-label" data-plan="premium">Premium</h1>

<div>
    <a href="#" class="cart-button" data-template="vasi">vasi</a>
    <a href="#" class="cart-button" data-template="paparouna">paparouna</a>
</div>

<div>
    <a href="#" class="cart-button" data-plan="essentials">essentials</a>
    <a href="#" class="cart-button" data-plan="advanced">advanced</a>
    <a href="#" class="cart-button" data-plan="premium">premium</a>
</div>

<a class="checkout-selection" data-template="true">Select a Template</a>
<a class="checkout-selection" data-plan="true">Select A Plan</a>

<a class="checkout-button">Checkout</a>

<style>
    .checkout-button {
        opacity: 0;
        transition: all 200ms;
    }
    .checkout-button.active {
        opacity: 1;
        transform: translateY(-50%);
    }
</style>
