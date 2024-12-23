<script setup lang="ts">
    // Interfaces and Types
    import type { Product } from "../interfaces/Product";

    defineProps<{
        product: Product;
    }>();
</script>

<template>
    <article class="product-card">
        <h3>{{ product.title || "Missing Title" }}</h3>
        <img :src="`src/assets/products/${product.image}`" />

        <p class="price">
            {{
                new Intl.NumberFormat("de-CH", {
                    style: "currency",
                    currency: "CHF",
                }).format(product.price)
            }}
        </p>
        <p class="category">{{ product.category }}</p>
        <p class="description">{{ product.description }}</p>

        <a href="#">Learn more</a>
    </article>
</template>

<style scoped>
    .product-card {
        text-align: center;
        display: flex;
        flex-direction: column;
        background-color: #ffffff;
        border-radius: 1rem;
        overflow: hidden;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        transition: transform 0.2s ease, box-shadow 0.2s ease;
    }

    .product-card .category {
        background-color: darkblue;
        border-radius: 20px;
        color: #f2f2f2;
        padding: 2px 32px;
        align-self: center;
    }

    .product-card .description {
        padding: 0 1rem;
    }

    .product-card img {
        width: 100%;
        height: 12rem;
        object-fit: cover;
    }

    .product-card h3 {
        font-size: 1.125rem;
        font-weight: 600;
        color: #2d3748;
        margin: 0.5rem 0;
    }

    .product-card p {
        color: #718096;
        line-height: 1.5;
    }

    .product-card .price {
        font-size: 1.25rem;
        font-weight: 700;
        margin-top: 1rem;
    }

    .product-card a {
        display: inline-block;
        margin-top: 1rem;
        padding: 0.75rem 1.5rem;
        background-color: #d85b12da;
        color: #ffffff;
        text-align: center;
        font-weight: 600;
        text-decoration: none;
        transition: background-color 0.4s ease;
    }

    .product-card a:hover {
        background-color: #d85b12;
    }
</style>
