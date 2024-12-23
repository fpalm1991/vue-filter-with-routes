<script setup lang="ts">
    import { ref, computed, watch } from "vue";
    import type { Product } from "../interfaces/Product";
    import ProductCard from "../components/ProductCard.vue";
    import { useRoute, useRouter } from "vue-router";

    const route = useRoute();
    const router = useRouter();

    // Testing data
    const products: Product[] = [
        {
            id: 1,
            title: "Sporty Sedan",
            price: 80000,
            image: "car-1.jpg",
            description: "A sleek and sporty sedan, perfect for city driving.",
            category: "Electric",
        },
        {
            id: 2,
            title: "Luxury SUV",
            price: 50000,
            image: "car-2.jpg",
            description: "A premium SUV for family trips and adventures.",
            category: "Convertible",
        },
        {
            id: 3,
            title: "Electric Coupe",
            price: 40000,
            image: "car-3.jpg",
            description: "An eco-friendly electric coupe with a modern design.",
            category: "Electric",
        },
        {
            id: 4,
            title: "Compact Hatchback",
            price: 15000,
            image: "car-4.jpg",
            description: "A compact hatchback that's efficient and affordable.",
            category: "Hatchback",
        },
        {
            id: 5,
            title: "Classic Convertible",
            price: 30000,
            image: "car-5.jpg",
            description: "A stylish convertible for sunny weekend drives.",
            category: "Electric",
        },
        {
            id: 6,
            title: "Luxury Sports Car",
            price: 60000,
            image: "car-6.jpg",
            description:
                "A high-performance luxury sports car with a sleek design.",
            category: "Sports",
        },
        {
            id: 7,
            title: "Rugged Pickup Truck",
            price: 45000,
            image: "car-7.jpg",
            description:
                "A durable pickup truck built for off-road adventures.",
            category: "Truck",
        },
        {
            id: 8,
            title: "Hybrid Crossover",
            price: 35000,
            image: "car-8.jpg",
            description:
                "A versatile hybrid crossover with impressive fuel efficiency.",
            category: "Hybrid",
        },
        {
            id: 9,
            title: "Vintage Roadster",
            price: 25000,
            image: "car-9.jpg",
            description: "A classic roadster with timeless style and charm.",
            category: "Convertible",
        },
        {
            id: 10,
            title: "Electric SUV",
            price: 55000,
            image: "car-10.jpg",
            description:
                "A spacious electric SUV with cutting-edge technology.",
            category: "Electric",
        },
    ];

    // Reactive state
    const selectedCategory = ref<string>(
        route.query.category?.toString() || "All"
    );

    const selectedMaxPrice = ref<number>(
        route.query.maxPrice
            ? parseInt(route.query.maxPrice.toString())
            : Math.max(...products.map((p) => p.price))
    );

    // Computed Properties
    const categories = computed(() => [
        "All",
        ...new Set(products.map((p) => p.category)),
    ]);

    const filteredByCategory = computed(() => {
        return selectedCategory.value === "All"
            ? products
            : products.filter((p) => p.category === selectedCategory.value);
    });

    const filteredProducts = computed(() => {
        return filteredByCategory.value.filter(
            (p) => p.price <= selectedMaxPrice.value
        );
    });

    const lowestPrice = computed(() => {
        return Math.min(...filteredByCategory.value.map((p) => p.price)) || 0;
    });

    const highestPrice = computed(() => {
        return Math.max(...filteredByCategory.value.map((p) => p.price)) || 0;
    });

    // Update URL depending on active filters
    watch(
        [selectedCategory, selectedMaxPrice],
        ([newCategory, newMaxPrice]) => {
            router.replace({
                query: {
                    ...route.query,
                    category: newCategory !== "All" ? newCategory : undefined,
                    maxPrice:
                        newMaxPrice !== highestPrice.value
                            ? newMaxPrice.toString()
                            : undefined,
                },
            });
        }
    );

    // Update price range dynamically
    watch(filteredByCategory, () => {
        selectedMaxPrice.value = highestPrice.value;
    });
</script>

<template>
    <h1>A very appealing title</h1>

    <div class="filters">
        <!-- Select Cateogry -->
        <select
            name="category"
            id="category"
            v-model="selectedCategory"
            class="custom-select"
        >
            <option
                v-for="category in categories"
                :value="category"
                :key="category"
            >
                {{ category || "Unknown category" }}
            </option>
        </select>

        <!-- Define Price Range -->
        <div v-show="filteredByCategory.length > 1" class="filters__price">
            <div class="filters__price__input">
                <input
                    type="range"
                    name="max-price"
                    id="max-price"
                    :min="lowestPrice"
                    :max="highestPrice"
                    step="10"
                    :value="selectedMaxPrice"
                    v-model="selectedMaxPrice"
                />
                <label for="max-price"
                    >Max. Price
                    {{
                        new Intl.NumberFormat("de-CH", {
                            style: "currency",
                            currency: "CHF",
                        }).format(selectedMaxPrice)
                    }}</label
                >
            </div>
        </div>
    </div>

    <TransitionGroup name="products" tag="div" class="products">
        <ProductCard
            v-for="product in filteredProducts"
            :product="product"
            :key="product.id"
        />
    </TransitionGroup>
</template>

<style scoped>
    .filters {
        display: flex;
        align-items: center;
        gap: 4rem;
        margin-bottom: 4rem;
    }

    .filters__price {
        display: flex;
        gap: 2rem;
    }

    .filters__price__input {
        display: flex;
        flex-direction: column;
    }

    .filters__price__input input {
        flex: 1;
    }

    .products {
        display: grid;
        gap: 4rem;

        @media (min-width: 680px) {
            gap: 2rem;
            grid-template-columns: repeat(2, 1fr);
        }

        @media (min-width: 1200px) {
            grid-template-columns: repeat(4, 1fr);
        }
    }

    .products-move,
    .products-enter-active,
    .products-leave-active {
        transition: all 0.4s ease;
    }

    .products-enter-from,
    .products-leave-to {
        opacity: 0;
        transform: translateY(-4rem);
    }
</style>
