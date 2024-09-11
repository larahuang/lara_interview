<template>
  <div v-if="isOpnAlert" class="alert">
    <div class="alert-main" :style="{ width: alertWidth + 'px' }">
      <div class="alert-header">
        <a @click.prevent="sendCloseAlert">
          <i class="fa-solid fa-xmark">X</i>
        </a>
      </div>
      <div class="modal-body flex justify-center flex-col">
        <div class="q-mb-xl">
          <q-input v-model="editTempData.name" label="姓名" />
          <q-input v-model="editTempData.age" label="年齡" />
        </div>
        <div class="flex justify-center w-1/3 m-auto">
          <a
            class="btn bg-stone-200 text-black mr-3 hover:bg-stone-300"
            @click.prevent="sendCloseAlert"
            >取消</a
          >
          <a
            class="btn bg-green-600 text-white hover:bg-green-800"
            @click.prevent="sendEdit"
            >確定</a
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
const props = defineProps({
  alertWidth: { type: String },
  isOpnAlert: { type: Boolean },
  editTempData: { type: Object },
});
const emit = defineEmits(['sendCloseAlert', 'sendEdit']);
const sendCloseAlert = () => {
  emit('sendCloseAlert');
};
const sendEdit = () => {
  emit('sendEdit');
};
</script>
