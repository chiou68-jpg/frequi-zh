<script setup lang="ts">
import type { BacktestResultInMemory } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
withDefaults(
  defineProps<{
    backtestResult: BacktestResultInMemory;
    selectedBacktestResultKey?: string;
    canUseModify?: boolean;
  }>(),
  {
    selectedBacktestResultKey: '',
    canUseModify: false,
  },
);
</script>

<template>
  <div class="flex flex-col me-2 text-start">
    <div class="font-bold">
      {{ backtestResult.metadata.strategyName }} - {{ backtestResult.strategy.timeframe }}
    </div>
    <div class="text-sm font-normal">
      {{ t('backtestSelectEntry.tradeCount') }}: {{ backtestResult.strategy.total_trades }} -
      {{ t('backtestSelectEntry.profit') }}:
      {{ formatPercent(backtestResult.strategy.profit_total) }}
    </div>
    <div v-if="canUseModify" class="text-sm font-normal" style="white-space: pre-wrap">
      {{ backtestResult.metadata.notes }}
    </div>
  </div>
</template>
