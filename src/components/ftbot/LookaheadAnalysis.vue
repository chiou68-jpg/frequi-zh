<script setup lang="ts">
import type { LookaheadAnalysisPayload, LookaheadResult } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();

const running = ref(false);
const result = ref<LookaheadResult | null>(null);
const statusMessage = ref('');

async function startAnalysis(payload: LookaheadAnalysisPayload) {
  running.value = true;
  result.value = null;
  statusMessage.value = '';
  try {
    const { job_id: jobId } = await botStore.activeBot.startLookaheadAnalysis(payload);
    const status = await botStore.activeBot.pollBgJob(jobId, 'lookahead_analysis');
    if (status.status === 'failed') {
      statusMessage.value = status.error || t('lookahead.failed');
      showAlert(statusMessage.value, 'error');
      return;
    }
    const analysis = await botStore.activeBot.getLookaheadAnalysisResult(jobId);
    if (analysis.status === 'ended') {
      result.value = analysis.result;
      statusMessage.value = analysis.status_msg;
    } else {
      statusMessage.value = analysis.status_msg || t('lookahead.failed');
      showAlert(statusMessage.value, 'error');
    }
  } catch (error) {
    console.error(error);
    showAlert(t('lookahead.runFailed'), 'error');
  } finally {
    running.value = false;
  }
}
</script>

<template>
  <div class="px-1 mx-auto w-full max-w-4xl lg:max-w-7xl">
    <BackgroundJobTracking class="mb-4" />
    <DraggableContainer :header="t('lookahead.title')" class="mx-1 p-4">
      <LookaheadAnalysisForm :running="running" @start="startAnalysis" />
    </DraggableContainer>
    <DraggableContainer v-if="result" :header="t('lookahead.result')" class="mx-1 mt-4 p-4">
      <LookaheadAnalysisResults :result="result" />
    </DraggableContainer>
  </div>
</template>
