<template>
  <Transition
    enter-active-class="transition duration-500"
    enter-from-class="opacity-0"
    enter-to-class="opacity-100"
    leave-active-class="transition duration-500"
    leave-to-class="opacity-0"
    leave-from-class="opacity-100"
    >
    <div class="flex items-center gap-4" v-if="localIsEditing">
      <input v-model="modifiedContent" type="text" class="block w-full px-4 py-2 mb-2 border-0 rounded-full ring-gray-3 ring-1 focus:ring-2 focus:ring-primary bg-gray-4 md:mb-0 placeholder:text-gray-3" :placeholder="placeholder">
      <button type="button" class="px-6 py-2 ml-auto text-white transition border-2 rounded-full border-gray-1 bg-gray-1 whitespace-nowrap disabled:hover:border-transparent disabled:hover:bg-gray-1 disabled:opacity-50 disabled:cursor-auto hover:border-primary-dark hover:bg-primary-dark"
      :disabled="modifiedContent?.length === 0"
      @click="handleSubmit">送出</button>
      <button type="button" class="px-6 py-2 ml-auto transition border-2 rounded-full text-gray-1 border-gray-1 whitespace-nowrap hover:bg-gray-1 hover:text-white"
      @click="emit('cancelEdit'); localIsEditing = false">關閉</button>
    </div>
    <div v-else-if="!localIsEditing && !isSubReply" class="flex justify-end">
      <button @click="startEditing" type="button" class="px-4 py-2 text-left bg-white border-2 rounded-3xl">
        <i class="mr-3 bi bi-arrow-90deg-left" />
        回覆
      </button>
    </div>
  </Transition>
</template>

<script setup>
import {
  defineEmits, defineProps, ref, watch,
} from 'vue';

const props = defineProps({
  originalContent: {
    type: String,
    default: '',
  },
  placeholder: {
    type: String,
    default: '請輸入評論...',
  },
  isEditing: {
    type: Boolean,
    default: false,
  },
  isSubReply: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits(['submitted', 'cancelEdit']);

const localIsEditing = ref(props.isEditing);

const modifiedContent = ref(props.originalContent);

watch(() => props.isEditing, (newVal) => {
  localIsEditing.value = newVal;
});

function startEditing() {
  localIsEditing.value = true;
}

function handleSubmit() {
  if (modifiedContent.value.trim().length === 0) {
    localIsEditing.value = false;
    modifiedContent.value = props.originalContent;
    emit('cancelEdit');
  } else {
    emit('submitted', modifiedContent.value);
    localIsEditing.value = false;
    modifiedContent.value = props.originalContent;
  }
}
</script>