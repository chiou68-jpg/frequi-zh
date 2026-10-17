<script setup lang="ts">
import type { StrategyBacktestResult } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const props = defineProps<{
  backtestResult: StrategyBacktestResult;
}>();

const backtestResultStats = computed(() => {
  const tmp = generateBacktestMetricRows(props.backtestResult);
  return formatObjectForTable({ value: tmp }, 'metric');
});

const backtestResultSettings = computed(() => {
  // Transpose Result into readable format
  const tmp = generateBacktestSettingRows(props.backtestResult);

  return formatObjectForTable({ value: tmp }, 'setting');
});
</script>

<template>
  <div class="px-0 w-full">
    <div class="flex justify-center">
      <h3 class="font-bold text-2xl mb-2">
        {{ t('backtestAnalysis.resultFor') }} {{ backtestResult.strategy_name }}
      </h3>
    </div>

    <div class="flex flex-col text-start ms-0 me-2 gap-2">
      <div class="flex flex-col xl:flex-row">
        <div class="px-0 px-xl-0 pe-xl-1 grow">
          <DraggableContainer :header="t('backtestAnalysis.strategySettings')">
            <UTable
              :data="backtestResultSettings"
              :columns="[
                { accessorKey: 'setting', header: t('backtestAnalysis.setting') },
                { accessorKey: 'value', header: t('backtestAnalysis.value') },
              ]"
            />
          </DraggableContainer>
        </div>
        <div class="px-0 xl:px-0 pt-2 xl:pt-0 xl:ps-1 grow">
          <DraggableContainer :header="t('backtestAnalysis.metrics')">
            <UTable
              :data="backtestResultStats"
              :columns="[
                { accessorKey: 'metric', header: t('backtestAnalysis.metric') },
                { accessorKey: 'value', header: t('backtestAnalysis.value') },
              ]"
            />
          </DraggableContainer>
        </div>
      </div>
      <BacktestResultTablePer
        :title="t('backtestAnalysis.perEnterTag')"
        :results="backtestResult.results_per_enter_tag"
        :stake-currency="backtestResult.stake_currency"
        :key-header="t('backtestAnalysis.enterTag')"
        :stake-currency-decimals="backtestResult.stake_currency_decimals"
      />

      <BacktestResultTablePer
        :title="t('backtestAnalysis.perExitReason')"
        :results="backtestResult.exit_reason_summary ?? []"
        :stake-currency="backtestResult.stake_currency"
        :key-header="t('backtestAnalysis.exitReason')"
        :stake-currency-decimals="backtestResult.stake_currency_decimals"
      />

      <BacktestResultTablePer
        v-if="backtestResult.mix_tag_stats"
        :title="t('backtestAnalysis.mixedTag')"
        :results="backtestResult.mix_tag_stats ?? []"
        :stake-currency="backtestResult.stake_currency"
        :key-headers="[t('backtestAnalysis.enterTag'), t('backtestAnalysis.exitTag')]"
        :stake-currency-decimals="backtestResult.stake_currency_decimals"
      />

      <BacktestResultTablePer
        :title="t('backtestAnalysis.perPair')"
        :results="backtestResult.results_per_pair"
        :stake-currency="backtestResult.stake_currency"
        :key-header="t('backtestAnalysis.pair')"
        :stake-currency-decimals="backtestResult.stake_currency_decimals"
      />
      <DraggableContainer v-if="backtestResult.periodic_breakdown" :header="t('backtestAnalysis.periodicBreakdown')">
        <BacktestResultPeriodBreakdown :periodic-breakdown="backtestResult.periodic_breakdown">
        </BacktestResultPeriodBreakdown>
      </DraggableContainer>

      <DraggableContainer :header="t('backtestAnalysis.singleTrades')">
        <TradeList
          :trades="backtestResult.trades"
          :show-filter="true"
          :stake-currency="backtestResult.stake_currency"
        />
      </DraggableContainer>
    </div>
  </div>
</template>
