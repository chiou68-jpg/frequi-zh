<script setup lang="ts">
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
defineProps<{
  history?: boolean;
}>();
const botStore = useBotStore();
</script>

<template>
  <div>
    <!-- <TradeList
      class="open-trades"
      :trades="openTrades"
      title="Open trades"
      :active-trades="true"
      empty-text="Currently no open trades."
    /> -->
    <CustomTradeList
      v-if="!history && !botStore.activeBot.detailTradeId"
      :trades="botStore.activeBot.openTrades"
      :title="t('mobileTrades.openTrades')"
      :active-trades="true"
      :stake-currency-decimals="botStore.activeBot.stakeCurrencyDecimals"
      :empty-text="t('mobileTrades.noOpen')"
    />
    <CustomTradeList
      v-if="history && !botStore.activeBot.detailTradeId"
      :trades="botStore.activeBot.closedTrades"
      :title="t('mobileTrades.tradeHistory')"
      :stake-currency-decimals="botStore.activeBot.stakeCurrencyDecimals"
      :empty-text="t('mobileTrades.noClosed')"
    />
    <div
      v-if="botStore.activeBot.detailTradeId && botStore.activeBot.tradeDetail"
      class="flex flex-col"
    >
      <UButton
        color="neutral"
        class="self-start my-1 ms-1"
        @click="botStore.activeBot.setDetailTrade(null)"
        :label="t('mobileTrades.back')"
        icon="mdi:arrow-left"
      />
      <TradeDetail
        :trade="botStore.activeBot.tradeDetail"
        :stake-currency="botStore.activeBot.stakeCurrency"
      />
    </div>
  </div>
</template>
