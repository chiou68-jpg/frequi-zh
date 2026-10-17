<script setup lang="ts">
import type { AuthStorageWithBotId } from '@/types';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
export interface LoginModalProps {
  loginInfo?: AuthStorageWithBotId;
}

defineProps<LoginModalProps>();
const emit = defineEmits<{
  close: [value: boolean];
}>();

function loginResult(result: boolean) {
  if (result) {
    // Only close if
    emit('close', result);
  }
}
</script>

<template>
  <UModal :title="t('loginModal.title')" :description="t('loginModal.desc')">
    <template #body>
      <BotLogin in-modal :existing-auth="loginInfo" @login-result="loginResult" />
    </template>
  </UModal>
</template>
