# Shopify product slider custom template section I built with js css and liquid for something a client of mine needed
So if you are using the dawn theme this will be really easy for you to implement:
<br />
<br />
Just create a .liquid file in your sections folder and copy the code from product-slider.liquid

``` html
{{ 'component-card.css' | asset_url | stylesheet_tag }}
{{ 'component-price.css' | asset_url | stylesheet_tag }}
{{ 'component-rte.css' | asset_url | stylesheet_tag }}
{{ 'component-rating.css' | asset_url | stylesheet_tag }}

<link rel="stylesheet" href="{{ 'component-slider.css' | asset_url }}" media="print" onload="this.media='all'">
<link rel="stylesheet" href="{{ 'template-collection.css' | asset_url }}" media="print" onload="this.media='all'">
<noscript>{{ 'component-slider.css' | asset_url | stylesheet_tag }}</noscript>
<noscript>{{ 'template-collection.css' | asset_url | stylesheet_tag }}</noscript>

{%- style -%}
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top | times: 0.75 | round: 0 }}px;
    padding-bottom: {{ section.settings.padding_bottom | times: 0.75 | round: 0 }}px;
  }

  @media screen and (min-width: 750px) {
    .section-{{ section.id }}-padding {
      padding-top: {{ section.settings.padding_top }}px;
      padding-bottom: {{ section.settings.padding_bottom }}px;
    }
  }
{%- endstyle -%}

{%- assign collection = collections[section.settings.collection] -%}
{%- assign product_limit = section.settings.product_count -%}
<div class="isolate gradient">
    <div class="collection section-{{ section.id }}-padding">
        <div class="justify-between pr-1p6rm-3r" style="padding-bottom: 10px;">
            {% if section.settings.title != blank %}
                <span class="product-slider--h2 pl-1rm-3r mtm2p"
                      style="line-height: 1;">
                    {{ section.settings.title | escape }}</span>
            {% endif %}
                <a href="{{ section.settings.collection.url }}"
                   class="button button--secondary mbm2p"
                   style="min-height: 3rem;
                          max-height: 5rem;
                          min-width: 1rem;
                          max-width: 12rem;">
                    {{ 'sections.featured_collection.view_all' | t }}</a>
        </div>
        <slider-component class="slider-mobile-gutter slider-component-desktop"
                          style="padding-left: 1rem;">
            <ul id="Slider-{{ section.id }}"
                class="grid product-grid contains-card 
                       contains-card--product contains-card--standard 
                       grid--4-col-desktop
                       grid--2-col-tablet-down slider 
                       slider--desktop slider--tablet grid--peek"
                role="list"
                aria-label="{{ 'general.slider.name' | t }}">
                {% for product in collection.products limit: product_limit %}
                    <li id="Slide-{{ section.id }}-{{ forloop.index }}"
                        class="grid__item slider__slide">
                        {% render 'card-product',
                            card_product: product,
                            media_aspect_ratio: section.settings.image_ratio,
                            show_secondary_image: section.settings.show_secondary_image,
                            section_id: section.id
                        %}
                {% endfor %}
            </ul>
            <div class="slider-buttons--product-slider no-js-hidden">
                <button
                    type="button"
                    class="rotation absolute left-8 xl:left-14 translate-y-1/2
                           w-5r h-5r flex justify-center items-center rounded-full
                           shadow-md z-10 bg-255-t-0-b-n-bs-h-bg-0-t-255-b-n-bs
                           display-none left-button"
                    name="previous"
                    aria-label="{{ 'general.slider.previous_slide' | t }}"
                    aria-controls="Slider-{{ section.id }}"
                >
                <svg className="font-bold" width="3rem" 
                     fill="none" stroke="currentColor" viewBox="0 0 24 24" 
                     xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" 
                      stroke-width="2.5" d="M9 5l7 7-7 7"></path>
                </svg>
                </button>
                <button
                    type="button"
                    class="absolute right-8 xl:right-14 translate-y-1/2
                           w-5r h-5r flex justify-center items-center rounded-full
                           shadow-md z-10 bg-255-t-0-b-n-bs-h-bg-0-t-255-b-n-bs
                           right-button"
                    name="next"
                    aria-label="{{ 'general.slider.next_slide' | t }}"
                    aria-controls="Slider-{{ section.id }}"
                >
                <svg className="font-bold" width="3rem" 
                     fill="none" stroke="currentColor" viewBox="0 0 24 24" 
                     xmlns="http://www.w3.org/2000/svg">
                <path stroke-linecap="round" stroke-linejoin="round" 
                      stroke-width="2.5" d="M9 5l7 7-7 7"></path>
                </svg>
                </button>
            </div>
        </slider-component>
    </div>
</div>

<script>
function centerOverlays() {
  const images = document.querySelectorAll('#Slider-{{ section.id }} img');
  let firstImageHeight = 0;
  images.forEach(image => {
    const height = image.height;
    if (height > firstImageHeight) {
      firstImageHeight = height;
    }
  });
  const overlays = document.querySelectorAll('.absolute');
  overlays.forEach(overlay => {
    const overlayHeight = overlay.offsetHeight;
    const topOffset = (firstImageHeight - overlayHeight) / 2;
    overlay.style.top = topOffset + 'px';
  });
}

document.addEventListener('DOMContentLoaded', function() {
    const leftButtons = document.querySelectorAll('.left-button');
    leftButtons.forEach((button, index) => {
      button.id = `left-button-${index + 1}`;
    });

    const rightButtons = document.querySelectorAll('.right-button');
    rightButtons.forEach((button, index) => {
      button.id = `right-button-${index + 1}`;
      button.addEventListener('click', function() {
        const leftBtn = document.getElementById(`left-button-${index + 1}`);
        leftBtn.classList.remove('display-none');
        centerOverlays();
      });
    });
  });

document.addEventListener("DOMContentLoaded", centerOverlays);
centerOverlays();
</script>

{% schema %}
{
    "name": "Slider produktů",
    "settings": [
        {
            "type": "text",
            "id": "title",
            "label": "Nadpis",
            "default": "Nadpis pro kolekci produktů"
        },
        {
            "type": "collection",
            "id": "collection",
            "label": "Kolekce"
        },
        {
            "type": "range",
            "id": "product_count",
            "min": 1,
            "max": 50,
            "label": "Množství produktů, které budou v slideru",
            "default": 10
        },
        {
            "type": "select",
            "id": "image_ratio",
            "options": [
                {
                    "value": "adapt",
                    "label": "t:sections.featured-collection.settings.image_ratio.options__1.label"
                },
                {
                    "value": "portrait",
                    "label": "t:sections.featured-collection.settings.image_ratio.options__2.label"
                },
                {
                    "value": "square",
                    "label": "t:sections.featured-collection.settings.image_ratio.options__3.label"
                }
            ],
            "default": "portrait",
            "label": "t:sections.featured-collection.settings.image_ratio.label"
        },
        {
            "type": "checkbox",
            "id": "show_secondary_image",
            "default": true,
            "label": "t:sections.featured-collection.settings.show_secondary_image.label"
        },
        {
            "type": "range",
            "id": "padding_top",
            "min": 0,
            "max": 100,
            "step": 4,
            "unit": "px",
            "label": "t:sections.all.padding.padding_top",
            "default": 28
        },
        {
            "type": "range",
            "id": "padding_bottom",
            "min": 0,
            "max": 100,
            "step": 4,
            "unit": "px",
            "label": "t:sections.all.padding.padding_bottom",
            "default": 0
        }
    ],
    "presets": [
        {
            "category": "Collection",
            "name": "Slider produktů",
            "settings": {}
        }
    ]
}
{% endschema %}
```

# Some of the names in this code are in czech because I am a czech freelancer so for me personally it's better to have this repo in czech because those are the guys I will mostly implement for.
<br />
But for you my friend here's all you have to change so you have everything named nicely in your language (just replace the inside of the schema with this):

``` html
{
    "name": "Product Slider",
    "settings": [
        {
            "type": "text",
            "id": "title",
            "label": "Heading",
            "default": "Heading"
        },
        {
            "type": "collection",
            "id": "collection",
            "label": "Collection"
        },
        {
            "type": "range",
            "id": "product_count",
            "min": 1,
            "max": 50,
            "label": "The amount of the products that will be in the slider",
            "default": 10
        },
        {
            "type": "select",
            "id": "image_ratio",
            "options": [
                {
                    "value": "adapt",
                    "label": "t:sections.featured-collection.settings.image_ratio.options__1.label"
                },
                {
                    "value": "portrait",
                    "label": "t:sections.featured-collection.settings.image_ratio.options__2.label"
                },
                {
                    "value": "square",
                    "label": "t:sections.featured-collection.settings.image_ratio.options__3.label"
                }
            ],
            "default": "portrait",
            "label": "t:sections.featured-collection.settings.image_ratio.label"
        },
        {
            "type": "checkbox",
            "id": "show_secondary_image",
            "default": true,
            "label": "t:sections.featured-collection.settings.show_secondary_image.label"
        },
        {
            "type": "range",
            "id": "padding_top",
            "min": 0,
            "max": 100,
            "step": 4,
            "unit": "px",
            "label": "t:sections.all.padding.padding_top",
            "default": 28
        },
        {
            "type": "range",
            "id": "padding_bottom",
            "min": 0,
            "max": 100,
            "step": 4,
            "unit": "px",
            "label": "t:sections.all.padding.padding_bottom",
            "default": 0
        }
    ],
    "presets": [
        {
            "category": "Collection",
            "name": "Product slider",
            "settings": {}
        }
    ]
}
```

# You'll also need to change the component-slider.css into

``` css
slider-component {
  --desktop-margin-left-first-item: max(5rem, calc((100vw - var(--page-width) + 10rem - var(--grid-desktop-horizontal-spacing)) / 2));
  position: relative;
  display: block;
}

slider-component.slider-component-full-width {
  --desktop-margin-left-first-item: 1.5rem;
}

@media screen and (max-width: 749px) {
  slider-component.page-width {
    padding: 0 1.5rem;
  }
}

@media screen and (min-width: 749px) and (max-width: 990px) {
  slider-component.page-width {
    padding: 0 5rem;
  }
}

@media screen and (max-width: 989px) {
  .no-js slider-component .slider {
    padding-bottom: 3rem;
  }
}

.slider__slide {
  --focus-outline-padding: 0.5rem;
  --shadow-padding-top: calc((var(--shadow-vertical-offset) * -1 + var(--shadow-blur-radius)) * var(--shadow-visible));
  --shadow-padding-bottom: calc((var(--shadow-vertical-offset) + var(--shadow-blur-radius)) * var(--shadow-visible));
  scroll-snap-align: start;
  flex-shrink: 0;
  padding-bottom: 0;
}

@media screen and (max-width: 749px) {
  .slider.slider--mobile {
    position: relative;
    flex-wrap: inherit;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
    scroll-padding-left: 1.5rem;
    -webkit-overflow-scrolling: touch;
    margin-bottom: 1rem;
  }

  /* Fix to show some space at the end of our sliders in all browsers */
  .slider--mobile:after {
    content: "";
    width: 0;
    padding-left: 1.5rem;
  }

  .slider.slider--mobile .slider__slide {
    margin-bottom: 0;
    padding-top: max(var(--focus-outline-padding), var(--shadow-padding-top));
    padding-bottom: max(var(--focus-outline-padding), var(--shadow-padding-bottom));
  }

  .slider.slider--mobile.contains-card--standard .slider__slide:not(.collection-list__item--no-media) {
    padding-bottom: var(--focus-outline-padding);
  }

  .slider.slider--mobile.contains-content-container .slider__slide {
    --focus-outline-padding: 0rem;
  }
}

@media screen and (min-width: 750px) {
  .slider.slider--tablet-up {
    position: relative;
    flex-wrap: inherit;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
    -webkit-overflow-scrolling: touch;
  }

  .slider.slider--tablet-up .slider__slide {
    margin-bottom: 0;
  }
}

@media screen and (max-width: 989px) {
  .slider.slider--tablet {
    position: relative;
    flex-wrap: inherit;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
    scroll-padding-left: 1.5rem;
    -webkit-overflow-scrolling: touch;
    margin-bottom: 1rem;
  }

  /* Fix to show some space at the end of our sliders in all browsers */
  .slider--tablet:after {
    content: "";
    width: 0;
    padding-left: 1.5rem;
    margin-left: calc(-1 * var(--grid-desktop-horizontal-spacing));
  }

  .slider.slider--tablet .slider__slide {
    margin-bottom: 0;
    padding-top: max(var(--focus-outline-padding), var(--shadow-padding-top));
    padding-bottom: max(var(--focus-outline-padding), var(--shadow-padding-bottom));
  }

  .slider.slider--tablet.contains-card--standard .slider__slide:not(.collection-list__item--no-media) {
    padding-bottom: var(--focus-outline-padding);
  }

  .slider.slider--tablet.contains-content-container .slider__slide {
    --focus-outline-padding: 0rem;
  }
}

.slider--everywhere {
  position: relative;
  flex-wrap: inherit;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  scroll-behavior: smooth;
  -webkit-overflow-scrolling: touch;
  margin-bottom: 1rem;
}

.slider.slider--everywhere .slider__slide {
  margin-bottom: 0;
  scroll-snap-align: center;
}

@media screen and (min-width: 990px) {
  .slider-component-desktop.page-width {
    max-width: none;
  }

  .slider--desktop {
    position: relative;
    flex-wrap: inherit;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
    -webkit-overflow-scrolling: touch;
    margin-bottom: 1rem;
    scroll-padding-left: var(--desktop-margin-left-first-item);
  }

  /* Fix to show some space at the end of our sliders in all browsers */
  .slider--desktop:after {
    content: "";
    width: 0;
    padding-left: 5rem;
    margin-left: calc(-1 * var(--grid-desktop-horizontal-spacing));
  }

  .slider.slider--desktop .slider__slide {
    margin-bottom: 0;
    padding-top: max(var(--focus-outline-padding), var(--shadow-padding-top));
    padding-bottom: max(var(--focus-outline-padding), var(--shadow-padding-bottom));
  }

  .slider-component-full-width .slider--desktop {
    scroll-padding-left: 1.5rem;
  }

  /* Fix to show some space at the end of our sliders in all browsers */
  .slider-component-full-width .slider--desktop:after {
    padding-left: 1.5rem;
  }

  .slider--desktop.grid--5-col-desktop .grid__item {
    width: calc( (100% - var(--desktop-margin-left-first-item)) / 5 - var(--grid-desktop-horizontal-spacing) * 2);
  }

  .slider--desktop.grid--4-col-desktop .grid__item {
    width: calc( (100% - var(--desktop-margin-left-first-item)) / 4 - var(--grid-desktop-horizontal-spacing) * 3);
  }

  .slider--desktop.grid--3-col-desktop .grid__item {
    width: calc( (100% - var(--desktop-margin-left-first-item)) / 3 - var(--grid-desktop-horizontal-spacing) * 4);
  }

  @media (min-width: 1900px) {
    .slider--desktop.grid--3-col-desktop .grid__item {
      width: calc( (115% - 1.15 * var(--desktop-margin-left-first-item)) / 3 - 1.15 * var(--grid-desktop-horizontal-spacing) * 4);
    }
  }

  .slider--desktop.grid--2-col-desktop .grid__item {
    width: calc( (100% - var(--desktop-margin-left-first-item)) / 2 - var(--grid-desktop-horizontal-spacing) * 5);
  }

  .slider--desktop.grid--1-col-desktop .grid__item {
    width: calc( (100% - var(--desktop-margin-left-first-item)) - var(--grid-desktop-horizontal-spacing) * 9);
  }

  .slider.slider--desktop.contains-card--standard .slider__slide:not(.collection-list__item--no-media) {
    padding-bottom: var(--focus-outline-padding);
  }

  .slider.slider--desktop.contains-content-container .slider__slide {
    --focus-outline-padding: 0rem;
  }
}

@media (prefers-reduced-motion) {
  .slider {
    scroll-behavior: auto;
  }
}

/* Scrollbar */

.slider {
  scrollbar-color: rgb(var(--color-foreground)) rgba(var(--color-foreground), 0.04);
  -ms-overflow-style: none;
  scrollbar-width: none;
}

.slider::-webkit-scrollbar {
  height: 0.4rem;
  width: 0.4rem;
  display: none;
}

.no-js .slider {
  -ms-overflow-style: auto;
  scrollbar-width: auto;
}

.no-js .slider::-webkit-scrollbar {
  display: initial;
}

.slider::-webkit-scrollbar-thumb {
  background-color: rgb(var(--color-foreground));
  border-radius: 0.4rem;
  border: 0;
}

.slider::-webkit-scrollbar-track {
  background: rgba(var(--color-foreground), 0.04);
  border-radius: 0.4rem;
}

.slider-counter {
  display: flex;
  justify-content: center;
  min-width: 4.4rem;
}

@media screen and (min-width: 750px) {
  .slider-counter--dots {
    margin: 0 1.2rem;
  }
}

.slider-counter__link {
  padding: 1rem;
}

@media screen and (max-width: 749px) {
  .slider-counter__link {
    padding: 0.7rem;
  }
}

.slider-counter__link--dots .dot {
  width: 1rem;
  height: 1rem;
  border-radius: 50%;
  border: 0.1rem solid rgba(var(--color-foreground), 0.5);
  padding: 0;
  display: block;
}

.slider-counter__link--active.slider-counter__link--dots .dot {
  background-color: rgb(var(--color-foreground));
}

@media screen and (forced-colors: active) {
  .slider-counter__link--active.slider-counter__link--dots .dot {
    background-color: CanvasText;
  }
}

.slider-counter__link--dots:not(.slider-counter__link--active):hover .dot {
  border-color: rgb(var(--color-foreground));
}

.slider-counter__link--dots .dot,
.slider-counter__link--numbers {
  transition: transform 0.2s ease-in-out;
}

.slider-counter__link--active.slider-counter__link--numbers,
.slider-counter__link--dots:not(.slider-counter__link--active):hover .dot,
.slider-counter__link--numbers:hover {
  transform: scale(1.1);
}

.slider-counter__link--numbers {
  color: rgba(var(--color-foreground), 0.5);
  text-decoration: none;
}

.slider-counter__link--numbers:hover {
  color: rgb(var(--color-foreground));
}

.slider-counter__link--active.slider-counter__link--numbers {
  text-decoration: underline;
  color: rgb(var(--color-foreground));
}

.slider-buttons {
  display: flex;
  align-items: center;
  justify-content: center;
}

/*
.slider-buttons--product-slider {
  display: flex;
  align-items: center;
  justify-content: center;
}
*/
@media (max-width: 768px) {
  .slider-buttons--product-slider {
    display: none;
  }
}

@media screen and (min-width: 990px) {
  .slider:not(.slider--everywhere):not(.slider--desktop) + .slider-buttons {
    display: none;
  }
}

@media screen and (max-width: 989px) {
  .slider--desktop:not(.slider--tablet) + .slider-buttons {
    display: none;
  }
}

@media screen and (min-width: 750px) {
  .slider--mobile + .slider-buttons {
    display: none;
  }
}

.slider-button {
  color: rgba(var(--color-foreground), 0.75);
  background: transparent;
  border: none;
  cursor: pointer;
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.slider-button:not([disabled]):hover {
  color: rgb(var(--color-foreground));
}

.slider-button .icon {
  height: 0.6rem;
}

.slider-button[disabled] .icon {
  color: rgba(var(--color-foreground), 0.3);
  cursor: not-allowed;
}

.slider-button--next .icon {
  transform: rotate(-90deg);
}

.slider-button--prev .icon {
  transform: rotate(90deg);
}

.slider-button--next:not([disabled]):hover .icon {
  transform: rotate(-90deg) scale(1.1);
}

.slider-button--prev:not([disabled]):hover .icon {
  transform: rotate(90deg) scale(1.1);
}
```

# And you will also need to add this in your css:

``` css
.justify-between {
  display: flex;
  justify-content: space-between;
}

.pr-1p6rm-3r {
  padding-right: 1.6rem;
}

@media (min-width: 768px) {
  .pr-1p6rm-3r {
    padding-right: 3rem;
  }
}

.pl-1rm-3r {
  padding-left: 1rem;
}

@media (min-width: 768px) {
  .pl-1rm-3r {
    padding-left: 3rem;
  }
}

@media (max-width: 768px) {
  .mtm2p {
    margin-top: 2px;
  }
  .mbm2p {
    margin-bottom: 2px;
  }
}

.absolute {
  position: absolute;
}

.right-8 {
  right: 2rem;
}

@media (min-width: 1280px) {
  .xl\:right-14 {
    right: 3.5rem;
  }
}

.left-8 {
  left: 2rem;
}

@media (min-width: 1280px) {
  .xl\:left-14 {
    left: 3.5rem;
  }
}

.top-1\/4 {
  top: 30%;
}

@media (min-width: 1600px) {
  .top-1\/4 {
    top: 35%;
  }
}

.w-5r {
  width: 5rem;
}

.h-5r {
  height: 5rem;
}

.flex {
  display: flex;
}

.justify-center {
  justify-content: center;
}

.items-center {
  align-items: center;
}

.rounded-full {
  border-radius: 9999px;
}

.shadow-md {
  --tw-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --tw-shadow-colored: 0 4px 6px -1px var(--tw-shadow-color), 0 2px 4px -2px var(--tw-shadow-color);
  box-shadow: var(--tw-ring-offset-shadow, 0 0 #0000), var(--tw-ring-shadow, 0 0 #0000), var(--tw-shadow);
}

.z-10 {
  z-index: 10;
}

.bg-255-t-0-b-n-bs-h-bg-0-t-255-b-n-bs {
  background-color: rgb(255, 255, 255);
  color:rgb(0, 0, 0);
  border: none;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.bg-255-t-0-b-n-bs-h-bg-0-t-255-b-n-bs:hover {
  background-color: rgb(0, 0, 0);
  color:rgb(255, 255, 255);
  border: none;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.font-bold {
  font-weight: 700;
}

.rotation {
  transform: rotate(180deg);
}

.display-none {
  display: none;
}
```

# And as last also change the onButtonClick(event) in class SliderComponent to this:

``` js
onButtonClick(event) {
    event.preventDefault();
    const step = event.currentTarget.classList.contains('bg-255-t-0-b-n-bs-h-bg-0-t-255-b-n-bs') ? 3 : 1;
    this.slideScrollPosition = event.currentTarget.name === 'next' ? this.slider.scrollLeft + (step * this.sliderItemOffset) : this.slider.scrollLeft - (step * this.sliderItemOffset);
    this.slider.scrollTo({
      left: this.slideScrollPosition
    });
  }
}
```

# Now. I am a tailwind person. I like tailwind. 
I obviously did not add the entire tailwind to this theme to not waste time and to not risk collusion but I used tailwind classes + got a bit inspired by tailwind's class ways, love it or hate it, that's why the html in liquid is the way it is, some love it, some hate it, but it's smarter to love it.
<br />
<br />
Besides all this there are some other changes from the regular dawn theme, u're welcome to try to use this theme.
