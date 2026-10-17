<script setup lang="ts">
import type { AllProfitStats } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const props = defineProps<{
  profitAll: AllProfitStats;
  stakeCurrency: string;
  stakeCurrencyDecimals: number;
}>();

const profit = computed(() => {
  if (!props.profitAll?.short) {
    return props.profitAll.all;
  }
  return props.profitAll[selectedOption.value];
});

const profitItems = computed(() => {
  if (!profit.value) return [];
  return [
    {
      metric: t('botProfit.roiClosed'),
      value: profit.value.profit_closed_coin
        ? `${formatPriceCurrency(
            profit.value.profit_closed_coin,
            props.stakeCurrency,
            props.stakeCurrencyDecimals,
          )} (${formatPercent(profit.value.profit_closed_ratio_mean, 2)})`
        : 'N/A',
    },
    {
      metric: t('botProfit.roiAll'),
      value: profit.value.profit_all_coin
        ? `${formatPriceCurrency(
            profit.value.profit_all_coin,
            props.stakeCurrency,
            props.stakeCurrencyDecimals,
          )} (${formatPercent(profit.value.profit_all_ratio_mean, 2)})`
        : 'N/A',
    },

    {
      metric: t('botProfit.totalTradeCount'),
      value: `${profit.value.trade_count ?? 0}`,
    },
    {
      metric: t('botProfit.botStarted'),
      value: profit.value.bot_start_timestamp,
      isTs: true,
    },
    {
      metric: t('botProfit.firstTrade'),
      value: profit.value.first_trade_timestamp,
      isTs: true,
    },
    {
      metric: t('botProfit.latestTrade'),
      value: profit.value.latest_trade_timestamp,
      isTs: true,
    },
    {
      metric: t('botProfit.winLoss'),
      value: `${profit.value.winning_trades ?? 0} / ${profit.value.losing_trades ?? 0}`,
    },
    {
      metric: t('botProfit.winrate'),
      value: `${profit.value.winrate ? formatPercent(profit.value.winrate) : 'N/A'}`,
    },
    {
      metric: t('botProfit.expectancy'),
      value: `${formatNumber(profit.value.expectancy, 2)} (${formatNumber(profit.value.expectancy_ratio, 2)})`,
    },
    {
      metric: t('botProfit.cagr'),
      value: `${formatPercent(profit.value.cagr, 2)}`,
    },
    {
      metric: t('botProfit.calmar'),
      value: `${formatNumber(profit.value.calmar, 2)}`,
    },
    {
      metric: t('botProfit.sharpe'),
      value: `${formatNumber(profit.value.sharpe, 2)}`,
    },
    {
      metric: t('botProfit.sortino'),
      value: `${formatNumber(profit.value.sortino, 2)}`,
    },
    {
      metric: t('botProfit.sqn'),
      value: `${formatNumber(profit.value.sqn, 2)}`,
    },
    {
      metric: t('botProfit.avgDuration'),
      value: `${profit.value.avg_duration ?? 'N/A'}`,
    },
    {
      metric: t('botProfit.bestPerforming'),
      value: profit.value.best_pair
        ? `${profit.value.best_pair}: ${formatPercent(profit.value.best_pair_profit_ratio, 2)}`
        : 'N/A',
    },
    {
      metric: t('botProfit.tradingVolume'),
      value: `${formatPriceCurrency(
        profit.value.trading_volume ?? 0,
        props.stakeCurrency,
        props.stakeCurrencyDecimals,
      )}`,
    },
    {
      metric: t('botProfit.profitFactor'),
      value: `${formatNumber(profit.value.profit_factor, 2)}`,
    },
    {
      metric: t('botProfit.maxDrawdown'),
      value: `${profit.value.max_drawdown ? formatPercent(profit.value.max_drawdown, 2) : 'N/A'} (${
        profit.value.max_drawdown_abs
          ? formatPriceCurrency(
              profit.value.max_drawdown_abs,
              props.stakeCurrency,
              props.stakeCurrencyDecimals,
            )
          : 'N/A'
      }) ${
        profit.value.max_drawdown_start_timestamp && profit.value.max_drawdown_end_timestamp
          ? t('botProfit.fromTo', {
              start: timestampms(profit.value.max_drawdown_start_timestamp),
              end: timestampms(profit.value.max_drawdown_end_timestamp),
            })
          : ''
      }`,
    },
    {
      metric: t('botProfit.currentDrawdown'),
      value: `${profit.value.current_drawdown ? formatPercent(profit.value.current_drawdown, 2) : 'N/A'} (${
        profit.value.current_drawdown_abs
          ? formatPriceCurrency(
              profit.value.current_drawdown_abs,
              props.stakeCurrency,
              props.stakeCurrencyDecimals,
            )
          : 'N/A'
      }) ${
        profit.value.current_drawdown_start_timestamp
          ? t('botProfit.since', { date: timestampms(profit.value.current_drawdown_start_timestamp) })
          : ''
      }`,
    },
  ];
});

const selectedOption = ref('all');
const options = [
  { value: 'all', text: t('botProfit.all') },
  { value: 'long', text: t('botProfit.long') },
  { value: 'short', text: t('botProfit.short') },
];
</script>

<template>
  <div>
    <div v-if="profitAll?.long && profitAll?.short" class="flex justify-between items-center">
      <span>{{ t('botProfit.profitsFor') }}</span>
      <USegmentedControl
        v-model="selectedOption"
        :items="options"
        label-key="text"
        value-key="value"
      ></USegmentedControl>
      <span>{{ t('botProfit.trades') }}</span>
    </div>

    <UTable
      class="text-start"
      :data="profitItems"
      :columns="[
        { accessorKey: 'metric', header: t('botProfit.metric') },
        { accessorKey: 'value', header: t('botProfit.value') },
      ]"
      :ui="{
        td: 'whitespace-normal',
      }"
    >
      <template #value-cell="{ row }">
        <DateTimeTZ v-if="row.original.isTs" :date="row.original.value" show-timezone />
        <template v-else>
          {{ row.original.value }}
        </template>
      </template>
    </UTable>
  </div>
</template>
