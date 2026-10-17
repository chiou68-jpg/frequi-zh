<script setup lang="ts">
import type { Lock } from '@/types';
import type { TableColumn } from '@nuxt/ui';
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
const botStore = useBotStore();

const columns: TableColumn<Lock>[] = [
  { accessorKey: 'pair', header: t('pairLock.pair') },
  { accessorKey: 'lock_end_timestamp', header: t('pairLock.until') },
  { accessorKey: 'reason', header: t('pairLock.reason') },
  { id: 'actions', header: t('pairLock.actions') },
];

function removePairLock(item: Lock) {
  console.log(item);
  if (item.id !== undefined) {
    botStore.activeBot.deleteLock(item.id);
  } else {
    showAlert(t('pairLock.noDeleteSupport'));
  }
}
</script>

<template>
  <div>
    <div class="mb-2">
      <label class="me-auto text-xl">{{ t('pairLock.title') }}</label>
      <UButton
        class="float-end"
        color="neutral"
        icon="mdi:refresh"
        @click="botStore.activeBot.getLocks"
      />
    </div>
    <UTable
      :data="botStore.activeBot.activeLocks"
      :columns="columns"
      :ui="{
        td: 'whitespace-normal',
      }"
    >
      <template #lock_end_timestamp-cell="{ row }">
        {{ timestampms(row.original.lock_end_timestamp) }}
      </template>
      <template #actions-cell="{ row }">
        <UButton
          class="btn-xs ms-1"
          size="sm"
          color="neutral"
          title="{{ t('pairLock.deleteLock') }}"
          icon="mdi:delete"
          @click="removePairLock(row.original)"
        />
      </template>
    </UTable>
  </div>
</template>
