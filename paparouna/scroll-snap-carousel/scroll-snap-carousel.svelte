<script>
    const poppy = (function () {
        let activeIndex = 0
        let container
        let elements

        function galleryWatch() {
            container = document.querySelector(".snap-container")
            elements = Array.from(container.querySelectorAll("img"))
            const intersections = elements.map(() => false)

            console.debug({ container, elements })

            function handleIntersect(entries) {
                entries.forEach(
                    ({ target, isIntersecting }) =>
                        (intersections[elements.indexOf(target)] = isIntersecting)
                )
                activeIndex = intersections.findIndex(Boolean)
                console.debug({ activeIndex, entries, intersections })
            }

            const observer = new IntersectionObserver(handleIntersect, {
                root: container,
                rootMargin: "0px",
                threshold: 0.75
            })

            elements.forEach(el => observer.observe(el))
        }

        return {
            goPrevious() {
                if (activeIndex > 0) container.scrollLeft = elements[activeIndex - 1].offsetLeft
            },
            goNext() {
                if (activeIndex < elements.length - 1)
                    container.scrollLeft = elements[activeIndex + 1].offsetLeft
            },
            galleryWatch
        }
    })()
    document.addEventListener("DOMContentLoaded", poppy.galleryWatch)
</script>

<!-- https://newbedev.com/css-scroll-snap-points-with-navigation-next-previous-buttons -->

<button class="gallery-arrow" type="button" onClick="poppy.goPrevious()">
    <img src="https://uploads-ssl.webflow.com/5d5c53cbee289025310e71ae/5d5c53cbee28901d1e0e735c_arrow-left-icon.svg" />
</button>
<button class="gallery-arrow" type="button" onClick="poppy.goNext()">
    <img src="https://uploads-ssl.webflow.com/5d5c53cbee289025310e71ae/5d5c53cbee28907ad00e7206_arrow-right-icon.svg" />
</button>


<style>
    .snap-container {
        scroll-snap-type: x mandatory;
        position: relative;
    }
    .snap-container > * {
        scroll-snap-align: start;
    }
    .gallery-arrow {
        width: 2rem;
        height: 2rem;
        padding: 0;
        background-color: transparent;
    }
    .gallery-arrow img {
        transition: opacity 400ms
        opacity: 0.5;
    }
    .gallery-arrow:hover img {
        opacity: 0.8;
    }
    .gallery-arrow + .gallery-arrow {
        margin-left: 1rem;
    }
    .gallery-lightbox-link {
        border-top-left-radius: 10px;
        border-bottom-left-radius: 10px;
    }
</style>
