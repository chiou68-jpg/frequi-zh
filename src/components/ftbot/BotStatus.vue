<script setup lang="ts">
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();
</script>

<template>
  <div v-if="botStore.activeBot.botState" class="p-4">
    <p class="mb-4">
      {{ t('status.runningFreqtrade') }} <strong>{{ botStore.activeBot.version }}</strong>
    </p>
    <p class="mb-4">
      {{ t('status.runningWith') }}
      <strong>
        {{ botStore.activeBot.botState.max_open_trades }}x{{
          botStore.activeBot.botState.stake_amount
        }}
        {{ botStore.activeBot.botState.stake_currency }}
      </strong>
      {{ t('status.on') }}
      <strong class="text-nowrap"
        >{{ botStore.activeBot.botState.exchange }}
        {{ botStore.activeBot.botState.demo_trading ? `(${t('status.demo')})` : '' }}</strong
      >
      {{ t('status.in') }}
      <strong
        >{{ botStore.activeBot.botState.trading_mode || 'spot' }}
        {{
          botStore.activeBot.botState.trading_mode !== 'spot'
            ? (botStore.activeBot.botState.margin_mode ?? '')
            : ''
        }}</strong
      >
      {{ t('status.marketsWithStrategy') }} <strong>{{ botStore.activeBot.botState.strategy }}</strong
      >.
    </p>
    <p v-if="'stoploss_on_exchange' in botStore.activeBot.botState" class="mb-4">
      {{ t('status.stoplossExchange') }}
      <strong>{{
        botStore.activeBot.botState.stoploss_on_exchange ? t('status.enabled') : t('status.disabled')
      }}</strong
      >.
    </p>
    <p class="mb-4">
      {{ t('status.currently') }} <strong>{{ botStore.activeBot.botState.state }}</strong
      >,
      <strong>{{ t('status.forceEntry') }} {{ botStore.activeBot.botState.force_entry_enable }}</strong>
    </p>
    <p>
      <strong>{{ botStore.activeBot.botState.dry_run ? t('status.dryRun') : t('status.live') }}</strong>
    </p>
    <USeparator class="my-2" />
    <p class="mb-4" v-if="botStore.activeBot.profit">
      {{ t('status.avgProfit', {
        mean: formatPercent(botStore.activeBot.profit.profit_all_ratio_mean),
        sum: formatPercent(botStore.activeBot.profit.profit_all_ratio_sum),
        count: botStore.activeBot.profit.trade_count,
        duration: botStore.activeBot.profit.avg_duration,
        best: botStore.activeBot.profit.best_pair,
      }) }}
    </p>
    <p v-if="botStore.activeBot.profit?.first_trade_timestamp" class="mb-4">
      <span v-if="botStore.activeBot.profit.bot_start_timestamp" class="block">
        {{ t('status.botStartDate') }}
        <strong>
          <DateTimeTZ :date="botStore.activeBot.profit.bot_start_timestamp" show-timezone />
        </strong>
      </span>
      <span class="block">
        {{ t('status.firstTrade') }}
        <strong>
          <DateTimeTZ :date="botStore.activeBot.profit.first_trade_timestamp" show-timezone />
        </strong>
      </span>
      <span class="block">
        {{ t('status.lastTrade') }}
        <strong>
          <DateTimeTZ :date="botStore.activeBot.profit.latest_trade_timestamp" show-timezone />
        </strong>
      </span>
    </p>
    <p>
      <span v-if="botStore.activeBot.profit?.profit_factor" class="block">
        {{ t('status.profitFactor') }}
        {{ formatNumber(botStore.activeBot.profit?.profit_factor, 2) }}
      </span>
      <span v-if="botStore.activeBot.profit?.trading_volume" class="block mb-4">
        {{ t('status.tradingVolume') }}
        {{
          formatPriceCurrency(
            botStore.activeBot.profit.trading_volume,
            botStore.activeBot.botState.stake_currency,
            botStore.activeBot.botState.stake_currency_decimals ?? 3,
          )
        }}
      </span>
    </p>
    <BaseCollapsible v-if="botStore.activeBot.strategy?.params" :title="t('status.strategyParams')">
      <StrategyParameters :strategy="botStore.activeBot.strategy" class="m-3" />
    </BaseCollapsible>
    <USeparator class="my-5" />
    <BotProfit
      class="mx-1"
      v-if="botStore.activeBot.profitAll"
      :profit-all="botStore.activeBot.profitAll"
      :stake-currency="botStore.activeBot.botState.stake_currency ?? 'USDT'"
      :stake-currency-decimals="botStore.activeBot.botState.stake_currency_decimals ?? 3"
    />
  </div>
</template>
