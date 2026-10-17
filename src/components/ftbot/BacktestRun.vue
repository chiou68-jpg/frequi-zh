<script setup lang="ts">
import type { BacktestPayload } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();
const btStore = useBtStore();

function clickBacktest() {
  const btPayload: BacktestPayload = {
    strategy: btStore.strategy,
    timerange: btStore.timerange,
    enable_protections: btStore.enableProtections,
  };
  if (btStore.maxOpenTrades) {
    btPayload.max_open_trades = btStore.maxOpenTrades;
  }
  if (btStore.stakeAmountUnlimited) {
    btPayload.stake_amount = 'unlimited';
  } else {
    const stakeAmountLoc = Number(btStore.stakeAmount);
    if (stakeAmountLoc) {
      btPayload.stake_amount = stakeAmountLoc.toString();
    }
  }

  const startingCapitalLoc = Number(btStore.startingCapital);
  if (startingCapitalLoc) {
    btPayload.dry_run_wallet = startingCapitalLoc;
  }

  if (btStore.selectedTimeframe) {
    btPayload.timeframe = btStore.selectedTimeframe;
  }
  if (btStore.selectedDetailTimeframe) {
    btPayload.timeframe_detail = btStore.selectedDetailTimeframe;
  }
  if (!btStore.allowCache) {
    btPayload.backtest_cache = 'none';
  }
  if (btStore.freqAI.enabled) {
    btPayload.freqaimodel = btStore.freqAI.model;
    if (btStore.freqAI.identifier !== '') {
      btPayload.freqai = { identifier: btStore.freqAI.identifier };
    }
  }

  botStore.activeBot.startBacktest(btPayload);
}
</script>

<template>
  <div class="mb-2">
    <span>{{ t('backtestRun.strategy') }}</span>
    <StrategySelect v-model="btStore.strategy"></StrategySelect>
  </div>
  <div
    class="grid grid-cols-2 border border-neutral-500 rounded-sm gap-y-2 gap-2 items-center p-1 pt-3"
    :disabled="botStore.activeBot.backtestRunning"
  >
    <!-- Backtesting parameters -->
    <h3 class="font-bold mb-2 col-span-2 text-center">{{ t('backtestRun.parameters') }}</h3>
    <label for="timeframe-select">{{ t('backtestRun.timeframe') }}:</label>
    <TimeframeSelect id="timeframe-select" v-model="btStore.selectedTimeframe" />
    <label for="timeframe-detail-select" class="flex justify-end items-center gap-2"
      >{{ t('backtestRun.detailTimeframe') }}:
      <InfoBox
        :hint="t('backtestRun.detailTimeframeHint')"
      />
    </label>
    <TimeframeSelect
      id="timeframe-detail-select"
      v-model="btStore.selectedDetailTimeframe"
      :below-timeframe="btStore.selectedTimeframe"
    />

    <label for="max-open-trades">{{ t('backtestRun.maxOpenTrades') }}:</label>
    <UInputNumber
      id="max-open-trades"
      v-model="btStore.maxOpenTrades"
      :placeholder="t('backtestRun.useStrategyDefault')"
      :increment="false"
      :decrement="false"
    ></UInputNumber>
    <label for="starting-capital">{{ t('backtestRun.startingCapital') }}:</label>
    <UInputNumber
      id="starting-capital"
      v-model="btStore.startingCapital"
      :placeholder="t('backtestRun.useConfigDefault')"
      :increment="false"
      :decrement="false"
      :step="10"
      :min="0"
      :stepSnapping="false"
      :format-options="{
        maximumFractionDigits: 5,
      }"
    ></UInputNumber>
    <label for="stake-amount-bool">{{ t('backtestRun.stakeAmount') }}:</label>
    <div class="flex items-center">
      <BaseCheckbox class="basis-1/3" id="stake-amount-bool" v-model="btStore.stakeAmountUnlimited"
        >{{ t('backtestRun.unlimitedStake') }}</BaseCheckbox
      >
      <UInputNumber
        id="stake-amount"
        v-model="btStore.stakeAmount"
        :placeholder="t('backtestRun.useStrategyDefault')"
        class="w-full"
        :step="10"
        :stepSnapping="false"
        :format-options="{
          maximumFractionDigits: 5,
        }"
        :min="0"
        :increment="false"
        :decrement="false"
        :disabled="btStore.stakeAmountUnlimited"
      ></UInputNumber>
    </div>

    <label for="enable-protections">{{ t('backtestRun.enableProtections') }}:</label>
    <BaseCheckbox id="enable-protections" v-model="btStore.enableProtections"></BaseCheckbox>
    <template v-if="botStore.activeBot.botFeatures.backtestFreqAI">
      <label for="enable-cache">{{ t('backtestRun.cacheResults') }}:</label>
      <BaseCheckbox id="enable-cache" v-model="btStore.allowCache"></BaseCheckbox>
    </template>

    <FreqAIModelInput
      v-if="botStore.activeBot.botFeatures.backtestFreqAI"
      v-model="btStore.freqAI"
    />

    <USeparator class="col-span-2 my-2" />
    <TimeRangeSelect
      v-model="btStore.timerange"
      :can-use-time="botStore.activeBot.botFeatures.timerangeWithTime"
      class="mx-auto mt-2 col-span-2"
    ></TimeRangeSelect>
  </div>

  <h3 class="mt-3 font-bold text-2xl">{{ t('backtestRun.summary') }}</h3>
  <div class="flex flex-wrap md:flex-nowrap justify-between md:justify-center mt-2">
    <UButton
      id="start-backtest"
      variant="solid"
      icon="mdi:play"
      :disabled="
        !btStore.canRunBacktest ||
        botStore.activeBot.backtestRunning ||
        !botStore.activeBot.canRunBacktest
      "
      class="mx-1"
      @click="clickBacktest"
    >
      {{ t('backtestRun.start') }}
    </UButton>
    <UButton
      color="neutral"
      icon="mdi:refresh"
      :disabled="botStore.activeBot.backtestRunning || !botStore.activeBot.canRunBacktest"
      class="mx-1"
      @click="botStore.activeBot.pollBacktest()"
    >
      {{ t('backtestRun.loadResult') }}
    </UButton>
    <UButton
      color="neutral"
      icon="mdi:stop"
      class="mx-1"
      :disabled="!botStore.activeBot.backtestRunning"
      @click="botStore.activeBot.stopBacktest()"
    >
      {{ t('backtestRun.stop') }}
    </UButton>
    <UButton
      color="neutral"
      class="mx-1"
      icon="mdi:delete"
      :disabled="botStore.activeBot.backtestRunning || !botStore.activeBot.canRunBacktest"
      @click="botStore.activeBot.removeBacktest()"
    >
      {{ t('backtestRun.reset') }}
    </UButton>
  </div>
</template>
<style lang="css" scoped>
label {
  @apply text-right;
}
</style>
