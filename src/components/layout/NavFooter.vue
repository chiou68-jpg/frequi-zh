<script setup lang="ts">
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();
</script>

<template>
  <footer class="md:hidden">
    <!-- Only visible on xs (phone) viewport! -->
    <hr class="my-0" />
    <div class="flex gap-2 justify-between px-2">
      <UButton
        v-if="!botStore.canRunBacktest"
        icon-pos="top"
        variant="link"
        active-class="underline"
        class="flex flex-col gap-1"
        to="/open_trades"
        :label="t('footer.trades')"
        icon="mdi:folder-open"
      />
      <UButton
        v-if="!botStore.canRunBacktest"
        icon-pos="top"
        variant="link"
        active-class="underline"
        class="flex flex-col gap-1"
        to="/trade_history"
        :label="t('footer.history')"
        icon="mdi:folder-lock"
      />
      <UButton
        v-if="!botStore.canRunBacktest"
        icon-pos="top"
        variant="link"
        class="flex flex-col gap-1"
        active-class="underline"
        to="/pairlist"
        :label="t('footer.pairlist')"
        icon="mdi:format-list-group"
      />
      <UButton
        v-if="!botStore.canRunBacktest"
        icon-pos="top"
        variant="link"
        active-class="underline"
        class="flex flex-col gap-1"
        to="/balance"
        :label="t('footer.balance')"
        icon="mdi:bank"
      />
      <UButton
        v-if="!botStore.canRunBacktest"
        icon-pos="top"
        variant="link"
        class="flex flex-col gap-1"
        active-class="underline"
        to="/dashboard"
        :label="t('footer.dashboard')"
        icon="mdi:view-dashboard-outline"
      />
    </div>
  </footer>
</template>
