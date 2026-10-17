<script setup lang="ts">
import type { BacktestFreqAIInput } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const freqAI = defineModel<BacktestFreqAIInput>({ required: true });
</script>

<template>
  <div class="flex justify-end items-center">
    <span class="me-2">{{ t('freqAIModelInput.enableFreqAI') }}:</span>
    <InfoBox
      :hint="t('freqAIModelInput.hint')"
    />
  </div>
  <BaseCheckbox id="enable-freqai" v-model="freqAI.enabled"></BaseCheckbox>

  <template v-if="freqAI.enabled">
    <label for="freqai-identifier">{{ t('freqAIModelInput.identifier') }}:</label>
    <UInput
      id="freqai-identifier"
      v-model="freqAI.identifier"
      :placeholder="t('freqAIModelInput.useConfigDefault')"
    ></UInput>
  </template>
  <template v-if="freqAI.enabled">
    <label for="freqai-model">{{ t('freqAIModelInput.model') }}:</label>
    <FreqaiModelSelect id="freqai-model" v-model="freqAI.model"></FreqaiModelSelect>
  </template>
</template>
