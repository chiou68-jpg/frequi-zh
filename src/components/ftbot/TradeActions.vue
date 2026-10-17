<script setup lang="ts">
import type { Trade, BotFeatures } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
withDefaults(
  defineProps<{
    botFeatures: BotFeatures;
    trade: Trade;
    enableForceEntry?: boolean;
  }>(),
  {
    enableForceEntry: false,
  },
);
defineEmits<{
  forceExit: [trade: Trade, type?: 'limit' | 'market'];
  forceExitPartial: [trade: Trade];
  cancelOpenOrder: [trade: Trade];
  reloadTrade: [trade: Trade];
  deleteTrade: [trade: Trade];
  forceEntry: [trade: Trade];
}>();
</script>

<template>
  <div class="flex flex-col gap-1">
    <UButton
      v-if="!botFeatures.forceExitParams"
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.forceExit')"
      :label="t('tradeActions.forceExit')"
      icon="mdi:close-box"
      @click="$emit('forceExit', trade)"
    />
    <UButton
      v-if="botFeatures.forceExitParams"
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.forceExitLimit')"
      :label="t('tradeActions.forceExitLimit')"
      icon="mdi:close-box"
      @click="$emit('forceExit', trade, 'limit')"
    />
    <UButton
      v-if="botFeatures.forceExitParams"
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.forceExitMarket')"
      :label="t('tradeActions.forceExitMarket')"
      icon="mdi:close-box"
      @click="$emit('forceExit', trade, 'market')"
    />
    <UButton
      v-if="botFeatures.forceEntryTag"
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.forceExitPartial')"
      :label="t('tradeActions.forceExitPartial')"
      icon="mdi:close-box-multiple"
      @click="$emit('forceExitPartial', trade)"
    />
    <UButton
      v-if="botFeatures.cancelOpenOrders && (trade.open_order_id || trade.has_open_orders)"
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.cancelOrders')"
      :label="t('tradeActions.cancelOrders')"
      icon="mdi:cancel"
      @click="$emit('cancelOpenOrder', trade)"
    />
    <UButton
      v-if="enableForceEntry"
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.increasePosition')"
      :label="t('tradeActions.increasePosition')"
      icon="mdi:plus-box-multiple-outline"
      @click="$emit('forceEntry', trade)"
    />
    <UButton
      v-if="botFeatures.reloadTrade"
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.reload')"
      :label="t('tradeActions.reload')"
      icon="mdi:reload-alert"
      @click="$emit('reloadTrade', trade)"
    />
    <UButton
      class="justify-start!"
      color="neutral"
      :title="t('tradeActions.deleteTrade')"
      :label="t('tradeActions.deleteTrade')"
      icon="mdi:delete"
      @click="$emit('deleteTrade', trade)"
    />
  </div>
</template>
