<script setup lang="ts">
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();

const botState = computed(() => botStore.activeBot?.botState ?? undefined);

const botItems = computed(() => [
  { label: t('system.botName'), value: botStore.activeBot?.botName || '-' },
  { label: t('system.strategy'), value: botState.value?.strategy ?? '-' },
  { label: t('system.exchange'), value: botState.value?.exchange ?? '-' },
  {
    label: t('system.tradingMode'),
    value: botState.value
      ? `${botState.value.trading_mode ?? 'spot'}${
          botState.value.trading_mode !== 'spot' && botState.value.margin_mode
            ? ` (${botState.value.margin_mode})`
            : ''
        }`
      : '-',
  },
  {
    label: t('system.stake'),
    value: botState.value
      ? `${botState.value.stake_amount} ${botState.value.stake_currency ?? ''}`
      : '-',
  },
  { label: t('system.maxOpenTrades'), value: botState.value?.max_open_trades ?? '-' },
  { label: t('system.state'), value: botState.value?.state ?? '-' },
  {
    label: t('system.mode'),
    value: botState.value?.dry_run === true ? t('system.dryRun') : t('system.live'),
  },
  { label: t('system.version'), value: botStore.activeBot?.version ?? '-' },
]);

const services = computed(() => [
  {
    name: t('system.hub'),
    desc: t('system.hubDesc'),
    file: 'controller/notification_hub.py',
    active: true,
  },
  {
    name: t('system.observer'),
    desc: t('system.observerDesc'),
    file: 'controller/freqtrade_observer.py',
    active: true,
  },
  {
    name: t('system.binding'),
    desc: t('system.bindingDesc'),
    file: 'controller/entry_lineage_binding_service.py',
    active: true,
  },
  {
    name: t('system.supervisor'),
    desc: t('system.supervisorDesc'),
    file: 'controller/supervisor.py',
    active: true,
  },
  {
    name: t('system.cleanKline'),
    desc: t('system.cleanKlineDesc'),
    file: 'controller/clean_kline.py',
    active: true,
  },
  {
    name: t('system.regimeGate'),
    desc: t('system.regimeGateDesc'),
    file: t('system.inStrategy'),
    active: true,
  },
  {
    name: t('system.xiaozhu'),
    desc: t('system.xiaozhuDesc'),
    file: 'controller/xiaozhu_reflection_worker.py',
    active: false,
  },
]);

const docs = computed(() => [
  { name: 'ACTIVE_STRATEGY_CONTRACT.md', desc: t('system.contract') },
  { name: 'V006_BINANCE_ACTIVE_RUNTIME_CONTRACT.md', desc: t('system.contract') },
  { name: 'WORKLOG_20261017.md', desc: t('system.worklog') },
  { name: 'SYSTEM_FLOW_FOR_CHATGPT.md', desc: t('system.flow') },
  { name: 'EXISTING_COMPONENT_MAP.json', desc: t('system.components') },
]);
</script>

<template>
  <div class="p-1 md:p-4 md:pe-2">
    <h1 class="text-xl mb-3">{{ t('system.title') }}</h1>

    <!-- Bot 運行狀態 -->
    <div class="mb-4 rounded-lg border border-primary bg-white dark:bg-gray-900 p-4">
      <h2 class="text-lg mb-2 font-bold">{{ t('system.botStatus') }}</h2>
      <dl class="grid grid-cols-1 md:grid-cols-2 gap-x-6 gap-y-1 text-sm">
        <div
          v-for="item in botItems"
          :key="item.label"
          class="flex justify-between border-b border-gray-200 dark:border-gray-700 py-1"
        >
          <dt class="text-gray-500">{{ item.label }}</dt>
          <dd class="font-medium">{{ item.value }}</dd>
        </div>
      </dl>
    </div>

    <!-- AI_Evo 自建服務 -->
    <div class="mb-4 rounded-lg border border-primary bg-white dark:bg-gray-900 p-4">
      <h2 class="text-lg mb-2 font-bold">{{ t('system.services') }}</h2>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
        <div
          v-for="s in services"
          :key="s.name"
          class="rounded-md border border-gray-300 dark:border-gray-700 p-3 text-sm"
        >
          <div class="flex items-center justify-between">
            <span class="font-semibold">{{ s.name }}</span>
            <span
              :class="s.active ? 'text-green-600' : 'text-red-500'"
              class="text-xs font-medium"
            >
              {{ s.active ? t('system.serviceActive') : t('system.serviceStopped') }}
            </span>
          </div>
          <p class="text-gray-500 mt-1">{{ s.desc }}</p>
          <code class="block text-xs text-gray-400 mt-1">{{ s.file }}</code>
        </div>
      </div>
    </div>

    <!-- 系統文件與契約 -->
    <div class="mb-4 rounded-lg border border-primary bg-white dark:bg-gray-900 p-4">
      <h2 class="text-lg mb-2 font-bold">{{ t('system.docs') }}</h2>
      <ul class="text-sm divide-y divide-gray-200 dark:divide-gray-700">
        <li v-for="d in docs" :key="d.name" class="py-2 flex justify-between gap-2">
          <span class="font-mono text-xs">{{ d.name }}</span>
          <span class="text-gray-500 whitespace-nowrap">{{ d.desc }}</span>
        </li>
      </ul>
    </div>

    <p class="text-xs text-gray-500">{{ t('system.note') }}</p>
  </div>
</template>
