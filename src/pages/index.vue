<script setup lang="ts">
import { useI18n } from 'vue-i18n';

const { t } = useI18n();
definePage({
  meta: {
    allowAnonymous: true,
  },
});
</script>

<template>
  <div class="mt-5">
    <div class="flex justify-center">
      <BotList class="max-w-xl" />
    </div>
    <USeparator class="my-5" />
    <AppIcon class="my-5 sm:w-80 sm:h-80 w-64 h-64" />

    <h1 class="font-bold text-2xl sm:text-4xl mb-4 transition-all">
      {{ t('indexPage.welcome') }} <AppText class="text-2xl! sm:text-4xl!" />
    </h1>
    <div>{{ t('indexPage.desc') }}</div>
    <USeparator class="my-5" />
    <p class="mb-2">
      {{ t('indexPage.help') }}
      <a
        class="text-primary underline cursor-pointer"
        href="https://www.freqtrade.io/en/latest/"
        target="_blank"
      >
        {{ t('indexPage.docs') }}
      </a>
      .
    </p>

    <p class="mb-5">{{ t('indexPage.haveFun') }}</p>
  </div>
</template>
