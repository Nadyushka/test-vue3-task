<template>
  <main>
    <div v-for="(item, index) in CardListOptions" :key="index" class="cards">
        <v-select
            class="card__sorting"
            label="Сортировка"
            variant="solo"
            bg-color="#D3D3D3FF"
            clearable
            item-value="sortType"
            :items="SortingOptions"
            @update:modelValue="(sortType) => setSortingForList(index, sortType)"
        >
          <template #selection="{ item }">
            {{ item.title  }}
            <v-icon
                :icon="item.raw.direction == 'asc' ? 'mdi-arrow-up' : 'mdi-arrow-down'"
                class="ml-1"/>
          </template>

          <template #item="{ item, props }">
            <v-list-item v-bind="props">
              <template v-slot:prepend>
                <v-icon
                    :icon="item.raw.direction == 'asc' ? 'mdi-arrow-up' : 'mdi-arrow-down'"
                    class="ml-1" />
              </template>
            </v-list-item>
          </template>
        </v-select>

      <CardList
          :options="item"
      />
    </div>
  </main>
</template>

<script setup>
  import { ref, provide } from 'vue';
  import axios from 'axios';
  import CardList from './components/CardList.vue';

  const firstList = ref([]);
  const secondList = ref([]);
  const lastList = ref([]);

  const CardListOptions = ref([
    {
      color: '#0aa6e362',
      title: 'Необработанные',
      id: 1,
    },
    {
      color: '#e86405bd',
      title: 'В работе',
      id: 2,
    },
    {
      color: '#9400d364',
      title: 'Завершенные',
      id: 3,
    },
  ]);

  const sortingTypes = {
    ratingAcs: 1,
    ratingDesc: 2
  }

  const SortingOptions = ref([
    {
      title: 'По рейтингу',
      sortType: sortingTypes.ratingAcs,
    },
    {
      title: 'По рейтингу',
      sortType: sortingTypes.ratingDesc,
    },
  ]);

  const setSortingForList = (listIndex, sortType) => {
    switch (listIndex) {
      case 0: {
        firstList.value = firstList.value.sort(sortCallback(sortType));
        return;
      }
      case 1: {
        secondList.value = secondList.value.sort(sortCallback(sortType));
        return;
      }
      case 2: {
        lastList.value = lastList.value.sort(sortCallback(sortType));
        return;
      }
      default:
        console.warn("Invalid list index");
    }
  };

  const sortCallback = (sortType) => (a, b) => {
    switch (sortType) {
      case sortingTypes.ratingAcs:
        return a.rating.rate - b.rating.rate;

      case sortingTypes.ratingDesc:
        return b.rating.rate - a.rating.rate;

      default:
        return a.id - b.id;
    }
  };

  const getAllCards = async () => {
    try {
      await axios
        .get('https://fakestoreapi.com/products')
        .then((res) => (firstList.value = res.data));
      console.log('getAllCards - успешно');
    } catch (error) {
      console.log(error);
      alert('Упс! Ошибка получения данных ;(');
    }
  };

  getAllCards();

  provide('firstList', firstList);
  provide('secondList', secondList);
  provide('lastList', lastList);
</script>

<style scoped>
  main {
    margin: 5% 0;
    display: flex;
    justify-content: center;
    gap: 5%;
    width: 100%;
  }

  .card__title {
    margin-bottom: 16px;
  }

</style>
