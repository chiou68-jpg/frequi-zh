<script setup lang="ts">
import { TimeSummaryOptions } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();
const settingsStore = useSettingsStore();

const props = defineProps<{
  multiBotView?: boolean;
}>();

const hasWeekly = computed(
  () => botStore.activeBot?.botFeatures?.weeklyMonthlyStats || props.multiBotView,
);

const periodicBreakdownSelections = computed(() => {
  const vals = [{ value: TimeSummaryOptions.daily, text: t('period.days') }];
  if (hasWeekly.value) {
    vals.push({ value: TimeSummaryOptions.weekly, text: t('period.weeks') });
    vals.push({ value: TimeSummaryOptions.monthly, text: t('period.months') });
  }
  return vals;
});

const absRelSelections = ref([
  { value: 'abs_profit', text: t('period.abs') },
  { value: 'rel_profit', text: t('period.rel') },
]);

const selectedStats = computed(() => {
  if (props.multiBotView) {
    switch (settingsStore.timeProfitPeriod) {
      case TimeSummaryOptions.weekly:
        return botStore.allWeeklyStatsSelectedBots;
      case TimeSummaryOptions.monthly:
        return botStore.allMonthlyStatsSelectedBots;
      default:
        return botStore.allDailyStatsSelectedBots;
    }
  }

  switch (settingsStore.timeProfitPeriod) {
    case TimeSummaryOptions.weekly:
      return botStore.activeBot.weeklyStats;
    case TimeSummaryOptions.monthly:
      return botStore.activeBot.monthlyStats;
    default:
      return botStore.activeBot.dailyStats;
  }
});

const selectedStatsSorted = computed(() => {
  // Sorted version for chart
  return {
    ...selectedStats.value,
    data: selectedStats.value.data
      ? Object.values(selectedStats.value.data).sort((a, b) => (a.date > b.date ? 1 : -1))
      : [],
  };
});

function refreshSummary() {
  if (props.multiBotView) {
    botStore.allGetTimeSummary(settingsStore.timeProfitPeriod);
  } else {
    botStore.activeBot.getTimeSummary(settingsStore.timeProfitPeriod);
  }
}

onMounted(() => {
  refreshSummary();
});

const tableColumns = computed(() => {
  const cols: { accessorKey: string; header: string }[] = [
    { accessorKey: 'date', header: t('period.day') },
    { accessorKey: 'abs_profit', header: t('period.profit') },
    {
      accessorKey: 'fiat_value',
      header: t('period.inFiat', { currency: selectedStats.value.fiat_display_currency }),
    },
    { accessorKey: 'trade_count', header: t('period.trades') },
  ];
  if (botStore.activeBot.botFeatures.advancedDailyMetrics) {
    cols.push({ accessorKey: 'rel_profit', header: t('period.profitPct') });
  }
  return cols;
});

watch(
  () => settingsStore.timeProfitPeriod,
  () => {
    refreshSummary();
  },
);
</script>

<template>
  <div class="flex flex-col h-full">
    <div v-if="!props.multiBotView" class="mb-2">
      <h3 class="me-auto inline text-xl">{{ hasWeekly ? t('period.periodBreakdown') : t('period.dailyBreakdown') }}</h3>
      <UButton class="float-end" color="neutral" icon="mdi:refresh" @click="refreshSummary" />
    </div>
    <div class="flex align-center justify-between">
      <USegmentedControl
        v-if="hasWeekly"
        id="order-direction"
        v-model="settingsStore.timeProfitPeriod"
        :items="periodicBreakdownSelections"
        size="sm"
        label-key="text"
        value-key="value"
      ></USegmentedControl>
      <USegmentedControl
        v-model="settingsStore.timeProfitPreference"
        name="radios-btn-select"
        size="sm"
        label-key="text"
        value-key="value"
        :items="absRelSelections"
      >
      </USegmentedControl>
    </div>

    <div
      v-if="
        !props.multiBotView ||
        botStore.selectedBotCount <= 1 ||
        settingsStore.timeProfitPreference !== 'rel_profit'
      "
    >
      <TimePeriodChart
        v-if="selectedStats"
        :daily-stats="selectedStatsSorted"
        :show-title="false"
        :profit-col="settingsStore.timeProfitPreference"
      />
    </div>
    <div v-else class="flex items-center justify-center h-full w-full p-2">
      {{ t('period.singleBotOnly') }}
    </div>
    <div v-if="!props.multiBotView">
      <UTable
        :data="selectedStats.data"
        :columns="tableColumns"        :ui="{
          td: 'whitespace-normal',
        }"
      >
        <template #abs_profit-cell="{ row }">
          {{ formatPrice(row.original.abs_profit, botStore.activeBot.stakeCurrencyDecimals) }}
        </template>
        <template #fiat_value-cell="{ row }">
          {{ formatPrice(row.original.fiat_value, 2) }}
        </template>
        <template #rel_profit-cell="{ row }">
          {{ formatPercent(row.original.rel_profit, 2) }}
        </template>
      </UTable>
    </div>
  </div>
</template>
