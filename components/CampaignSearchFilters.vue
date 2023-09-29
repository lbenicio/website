<template>
  <ul class="search_filters">
    <li
      v-for="gender in genders"
      :key="gender.webcode"
      class="search_filters__option"
    >
      <button
        class="search_filters__option__button"
        @click.prevent.stop="selectGender(gender)"
        :class="{ search_filters__option__button_selected: gender.active }"
      >
        {{ gender.label[locale] }}
      </button>
    </li>

    <li
      v-for="category in categories"
      :key="category.webcode"
      class="search_filters__option"
    >
      <button
        class="search_filters__option__button"
        @click.prevent.stop="toggleCategory(category)"
        :class="{ search_filters__option__button_selected: category.active }"
      >
        {{ category.label[locale] }}
      </button>
    </li>
  </ul>
</template>

<script setup>
import { useCampaignSearchStore } from "~/stores/campaignSearch";
import { useAurazApiFetch } from "~/composables/useAurazApiFetch";

const { locale } = useI18n();

const { selectGender, toggleCategory } = useCampaignSearchStore();

const { data: gendersResponse, error: failed_genders } =
  await useAurazApiFetch(`/v1/genders`);
const { data: categoriesResponse, error: failed_categories } =
  await useAurazApiFetch(`/v1/categories`);

const genders = computed(() => gendersResponse.value?.data || []);
const categories = computed(() => categoriesResponse.value?.data || []);

if (Boolean(failed_genders.value) && Boolean(failed_categories.value)) {
  throw createError({
    statusCode: 404,
    statusMessage: `Failed to load search filters`,
  });
}
</script>

<style>
.search_filters {
  @apply w-full;
  @apply grid place-items-center;
  @apply grid-cols-2 md:grid-cols-4;
  @apply gap-2;
}

.search_filters__option {
  @apply w-full h-full;
}

.search_filters__option__button {
  @apply w-full h-full;
  @apply p-2;

  @apply text-dark;
  @apply bg-gray-200;

  @apply rounded-md;
  @apply transition-colors ease-in-out duration-0;

  @apply flex flex-col justify-center items-center;
}

.search_filters__option__button_selected {
  @apply text-light;
  @apply bg-gradient-to-br from-primary from-30% to-secondary to-70%;
}
</style>
