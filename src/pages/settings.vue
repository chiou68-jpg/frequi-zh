<script setup lang="ts">
import { FtWsMessageTypes } from '@/types/wsMessageTypes';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const settingsStore = useSettingsStore();
const colorStore = useColorStore();
const layoutStore = useLayoutStore();

const timezoneOptions = ['UTC', Intl.DateTimeFormat().resolvedOptions().timeZone];
const openTradesOptions = [
  { value: OpenTradeVizOptions.showPill, text: t('settings.showPill') },
  { value: OpenTradeVizOptions.asTitle, text: t('settings.showInTitle') },
  { value: OpenTradeVizOptions.noOpenTrades, text: t('settings.noOpenTrades') },
];
const colorPreferenceOptions = [
  { value: ColorPreferences.GREEN_UP, text: t('settings.greenUp') },
  { value: ColorPreferences.RED_UP, text: t('settings.redUp') },
];

const resetDynamicLayout = () => {
  layoutStore.resetTradingLayout();
  layoutStore.resetDashboardLayout();
  showAlert(t('settings.layoutsReset'));
};
</script>

<template>
  <UCard class="mx-auto mt-3 p-4 max-w-4xl">
    <template #header><span class="text-2xl font-bold">{{ t('settings.title') }}</span></template>
    <div class="flex flex-col gap-4 text-start dark:text-neutral-300">
      <p class="text-left">{{ t('settings.uiVersion') }}: {{ settingsStore.uiVersion }}</p>

      <div class="border border-neutral-400 rounded-sm p-4 space-y-4">
        <h4 class="text-xl font-semibold">{{ t('settings.uiSettings') }}</h4>

        <BaseCheckbox v-model="layoutStore.layoutLocked" class="space-y-1">
          {{ t('settings.lockLayouts') }}
          <template #hint>
            {{ t('settings.lockLayoutsHint') }}
          </template>
        </BaseCheckbox>

        <div class="flex flex-row items-center gap-2 space-y-2">
          <UButton color="neutral" size="md" class="mb-0" @click="resetDynamicLayout"
            >{{ t('settings.resetLayout') }}</UButton
          >
          <small class="text-sm block text-neutral-600 dark:text-neutral-400"
            >{{ t('settings.resetLayoutHint') }}</small
          >
        </div>

        <USeparator />

        <div class="space-y-1">
          <label class="block text-sm">{{ t('settings.showOpenTrades') }}</label>
          <USelect
            v-model="settingsStore.openTradesInTitle"
            :items="openTradesOptions"
            label-key="text"
            value-key="value"
            class="w-full"
          />
          <small class="text-sm text-neutral-600 dark:text-neutral-400"
            >{{ t('settings.showOpenTradesHint') }}</small
          >
        </div>

        <div class="space-y-1">
          <label class="block text-sm">{{ t('settings.utcTimezone') }}</label>
          <USelect v-model="settingsStore.timezone" :items="timezoneOptions" class="w-full" />
          <small class="text-sm text-neutral-600 dark:text-neutral-400"
            >{{ t('settings.utcTimezoneHint') }}</small
          >
        </div>

        <BaseCheckbox v-model="settingsStore.backgroundSync" class="space-y-1">
          {{ t('settings.backgroundSync') }}
          <template #hint> {{ t('settings.backgroundSyncHint') }} </template>
        </BaseCheckbox>

        <BaseCheckbox v-model="settingsStore.confirmDialog" class="space-y-1">
          {{ t('settings.confirmDialog') }}
          <template #hint
            >{{ t('settings.confirmDialogHint') }}<br />
            {{ t('settings.confirmDialogHint2') }}
            <i-mdi-run-fast class="text-yellow-300 inline" />
            <i-mdi-alert class="text-yellow-300 inline" />
            {{ t('settings.confirmDialogHint3') }}
          </template>
        </BaseCheckbox>

        <BaseCheckbox v-model="settingsStore.multiPaneButtonsShowText" class="space-y-1">
          {{ t('settings.multiPaneText') }}
          <template #hint
            >{{ t('settings.multiPaneTextHint') }}</template
          >
        </BaseCheckbox>
      </div>

      <div class="border border-neutral-400 rounded-sm p-4 space-y-4">
        <h4 class="text-lg font-semibold">{{ t('settings.chartSettings') }}</h4>

        <div class="space-y-1">
          <label class="block text-sm">{{ t('settings.chartScaleSide') }}</label>
          <URadioGroup
            v-model="settingsStore.chartLabelSide"
            :items="[
              { label: t('settings.left'), value: 'left' },
              { label: t('settings.right'), value: 'right' },
            ]"
            orientation="horizontal"
          />
          <small class="text-sm text-neutral-600 dark:text-neutral-400">
            {{ t('settings.chartScaleSideHint') }}
          </small>
        </div>

        <BaseCheckbox v-model="settingsStore.useHeikinAshiCandles" class="space-y-1">
          {{ t('settings.heikinAshi') }}
          <template #hint>{{ t('settings.heikinAshiHint') }}</template>
        </BaseCheckbox>

        <BaseCheckbox v-model="settingsStore.useReducedPairCalls" class="space-y-1">
          {{ t('settings.reducedPairCalls') }}
          <template #hint
            >{{ t('settings.reducedPairCallsHint') }}</template
          >
        </BaseCheckbox>

        <div>
          <p>{{ t('settings.defaultCandles') }}</p>
          <div class="flex flex-row gap-5 w-full items-center">
            <USlider
              v-model="settingsStore.chartDefaultCandleCount"
              class="flex-1"
              :step="50"
              :min="100"
              :max="2000"
            />
            <UInputNumber
              v-model="settingsStore.chartDefaultCandleCount"
              :step="50"
              :min="100"
              :max="2000"
              size="sm"
            />
          </div>
        </div>

        <div class="space-y-1">
          <label class="block">{{ t('settings.candleColorPref') }}</label>
          <div class="flex flex-row gap-5 items-center">
            <URadioGroup
              v-model="colorStore.colorPreference"
              :items="colorPreferenceOptions"
              label-key="text"
              value-key="value"
              orientation="horizontal"
            >
              <template #label="{ item }">
                <div class="flex items-center">
                  <span class="mr-2">{{ item.text }}</span>
                  <UIcon
                    name="mdi:arrow-up-thin"
                    :color="
                      item.value === ColorPreferences.GREEN_UP
                        ? colorStore.colorProfit
                        : colorStore.colorLoss
                    "
                    class="-ml-2 size-5"
                  />
                  <UIcon
                    name="mdi:arrow-down-thin"
                    :color="
                      item.value === ColorPreferences.GREEN_UP
                        ? colorStore.colorLoss
                        : colorStore.colorProfit
                    "
                    class="-ml-2 size-5"
                  />
                </div>
              </template>
            </URadioGroup>
          </div>
        </div>
      </div>

      <div class="border rounded-sm border-neutral-400 p-4 space-y-4">
        <h4 class="text-lg font-semibold">{{ t('settings.notificationSettings') }}</h4>
        <div class="space-y-2">
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.entryFill]">
            {{ t('settings.entryNotifications') }}
          </BaseCheckbox>
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.exitFill]">
            {{ t('settings.exitNotifications') }}
          </BaseCheckbox>
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.entryCancel]">
            {{ t('settings.entryCancelNotifications') }}
          </BaseCheckbox>
          <BaseCheckbox v-model="settingsStore.notifications[FtWsMessageTypes.exitCancel]">
            {{ t('settings.exitCancelNotifications') }}
          </BaseCheckbox>
        </div>
      </div>

      <div class="border rounded-sm border-neutral-400 p-4 space-y-4">
        <h4 class="text-lg font-semibold">{{ t('settings.backtestSettings') }}</h4>
        <div>
          <label for="backtestMetrics" class="block">{{ t('settings.backtestMetrics') }}</label>
          <USelectMenu
            multiple
            id="backtestMetrics"
            v-model="settingsStore.backtestAdditionalMetrics"
            :items="availableBacktestMetrics"
            label-key="header"
            value-key="field"
            class="w-full"
            display="chip"
          />
          <small class="text-sm text-neutral-600 dark:text-neutral-400"
            >{{ t('settings.backtestMetricsHint') }}</small
          >
        </div>
      </div>
    </div>
  </UCard>
</template>
