<script setup lang="ts">
import type { ForceExitPayload } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();
const { confirm } = useConfirmBox();

const { forceEntryDialog } = useForceTrade();

const isRunning = computed((): boolean => {
  return botStore.activeBot.botState?.state === 'running';
});

async function handleStopBot() {
  const result = await confirm({
    title: t('controls.stopBotTitle'),
    message: t('controls.stopBotMsg'),
  });
  if (result) {
    botStore.activeBot.stopBot();
  }
}

async function handleStopBuy() {
  if (
    await confirm({
      title: t('controls.pauseTitle'),
      message: t('controls.pauseMsg'),
    })
  ) {
    botStore.activeBot.stopBuy();
  }
}

async function handleReloadConfig() {
  if (
    await confirm({
      title: t('controls.reloadConfigTitle'),
      message: t('controls.reloadConfigMsg'),
    })
  ) {
    botStore.activeBot.reloadConfig();
  }
}

async function handleForceExit() {
  if (
    await confirm({
      title: t('controls.forceExitTitle'),
      message: t('controls.forceExitMsg'),
    })
  ) {
    const payload: ForceExitPayload = {
      tradeid: 'all',
      // TODO: support ordertype (?)
    };
    botStore.activeBot.forceexit(payload);
  }
}

async function handleForceEntry() {
  await forceEntryDialog({
    pair: botStore.activeBot.selectedPair,
  });
}
</script>

<template>
  <div class="flex flex-row gap-1">
    <UButton
      size="xl"
      color="neutral"
      :disabled="!botStore.activeBot.isTrading || isRunning"
      :title="t('controls.startTrading')"
      icon="mdi:play"
      @click="botStore.activeBot.startBot()"
    />
    <UButton
      size="xl"
      color="neutral"
      :disabled="!botStore.activeBot.isTrading || !isRunning"
      :title="t('controls.stopTrading')"
      icon="mdi:stop"
      @click="handleStopBot()"
    />
    <UButton
      size="xl"
      color="neutral"
      :disabled="!botStore.activeBot.isTrading || !isRunning"
      :title="t('controls.pause')"
      icon="mdi:pause"
      @click="handleStopBuy()"
    />
    <UButton
      size="xl"
      color="neutral"
      :disabled="!botStore.activeBot.isTrading"
      :title="t('controls.reloadConfig')"
      icon="mdi:reload"
      @click="handleReloadConfig()"
    />
    <UButton
      color="neutral"
      size="xl"
      :disabled="!botStore.activeBot.isTrading"
      :title="t('controls.forceExitAll')"
      icon="mdi:close-box-multiple"
      @click="handleForceExit()"
    />
    <UButton
      v-if="botStore.activeBot.botState && botStore.activeBot.botState.force_entry_enable"
      size="xl"
      color="neutral"
      :disabled="!botStore.activeBot.isTrading || !isRunning"
      :title="t('controls.forceEnter')"
      icon="mdi:plus-box-multiple-outline"
      @click="handleForceEntry"
    />
    <UButton
      v-if="botStore.activeBot.isWebserverMode && false"
      size="xl"
      color="neutral"
      :disabled="botStore.activeBot.isTrading"
      :title="t('controls.startTradingMode')"
      icon="mdi:play"
      @click="botStore.activeBot.startTrade()"
    />
  </div>
</template>
