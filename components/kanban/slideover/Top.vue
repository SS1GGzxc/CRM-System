<script setup lang="ts">
import dayjs from 'dayjs';


const store = useDealSlideStore()

</script>


<template>
  <div class='border-border bg-black/20 rounded p-3'>
    <div class='uppercase bold text-xl mb-4'>
      <KanbanSlideoverLable label-text="Наименование">
        <h2>{{ store.card?.name }}</h2>
      </KanbanSlideoverLable>
      <KanbanSlideoverLable label-text="Сумма">
        {{ convertCurrency(store.card?.price || 0) }}
      </KanbanSlideoverLable>
      <KanbanSlideoverLable label-text="Статус">
        <UiBadge variant="outline">
          {{ store.card?.state }}
        </UiBadge>
      </KanbanSlideoverLable>
      <KanbanSlideoverLable label-text="Клиент">
        {{ store.card?.companyName }}
      </KanbanSlideoverLable>
      <KanbanSlideoverLable label-text="Дата создания">
        {{ dayjs(store.card?.$createdAt).format('DD MMMM YYYY') }}
      </KanbanSlideoverLable>
    </div>
  </div>
</template>
