<script setup lang="ts">
import type { PeriodicBreakdown } from '@/types';
import type { TableColumn } from '@nuxt/ui';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const props = defineProps<{
  periodicBreakdown: PeriodicBreakdown;
}>();

const periodicBreakdownSelections = computed(() => {
  const res = [
    { value: 'day', label: t('periodBreakdown.days') },
    { value: 'week', label: t('periodBreakdown.weeks') },
    { value: 'month', label: t('periodBreakdown.months') },
  ];
  if (props.periodicBreakdown.year) {
    res.push({ value: 'year', label: t('periodBreakdown.years') });
  }
  if (props.periodicBreakdown.weekday) {
    res.push({ value: 'weekday', label: t('periodBreakdown.weekday') });
  }

  return res;
});

const periodicBreakdownPeriod = ref<string>('month');

type PeriodRow = {
  date: string;
  trades?: number;
  profit_abs?: number;
  profit_factor?: number;
  wins?: number;
  draws?: number;
  losses?: number;
  loses?: number;
};

const columns: TableColumn<PeriodRow>[] = [
  { accessorKey: 'date', header: t('periodBreakdown.date') },
  { accessorKey: 'trades', header: t('periodBreakdown.trades') },
  { accessorKey: 'profit_abs', header: t('periodBreakdown.totalProfit') },
  { accessorKey: 'profit_factor', header: t('periodBreakdown.profitFactor') },
  { accessorKey: 'wins', header: t('periodBreakdown.wins') },
  { accessorKey: 'draws', header: t('periodBreakdown.draws') },
  { accessorKey: 'losses', header: t('periodBreakdown.losses') },
  { id: 'win_rate', header: t('periodBreakdown.winRate') },
];
</script>

<template>
  <USegmentedControl
    v-model="periodicBreakdownPeriod"
    :items="periodicBreakdownSelections"
    value-key="value"
    size="md"
    class="m-2"
  ></USegmentedControl>
  <UTable :data="periodicBreakdown[periodicBreakdownPeriod]" :columns="columns">
    <template #trades-cell="{ row }">
      {{ row.original.trades ?? 'N/A' }}
    </template>
    <template #profit_abs-cell="{ row }">
      {{ formatNumber(row.original.profit_abs, 2) }}
    </template>
    <template #profit_factor-cell="{ row }">
      {{ formatPrice(row.original.profit_factor ?? null, 2) }}
    </template>
    <template #losses-cell="{ row }">
      {{ row.original.loses ?? row.original.losses ?? 'N/A' }}
    </template>
    <template #win_rate-cell="{ row }">
      {{
        formatPercent(
          row.original.wins! /
            (row.original.wins! +
              row.original.draws! +
              (row.original.loses ?? row.original.losses ?? 0)),
          2,
        )
      }}
    </template>
  </UTable>
</template>
