
<template>
  <form class="form" action="">
    <h3 class="form-title">{{ title }}</h3>
    <div>
      <label 
        v-if="!fileComputed" for="input-file"
        tabindex="0" @keydown.space.prevent="openFileDialog" @keydown.enter.prevent="openFileDialog"
        class="custom-file-upload" :class="{notActive: !active}"
      >
        Выбрать файл
      </label>
      <button v-else  @click="clearImg" class="custom-file-upload">
        Удалить файл
      </button>
      <span v-if="fileComputed" class="form-file-name"> {{ fileComputed }}</span>
      <span v-else class="form-file-name form-file-name-none">Файл не выбран</span>
      <input 
        type="file" id="input-file" ref="fileInput"
        @change="downloadImg" class="custom-file-input">
    </div>
    <span v-if="!v$.imageName.$error" class="form-footer">
      {{ hint }}
    </span>
    <span v-else class="form-footer error"> Error message</span>
  </form>
</template>

<script setup lang="ts">
import { defineEmits, ref, computed } from 'vue'
import { useVuelidate } from '@vuelidate/core'
import { required } from '@vuelidate/validators'

const props = defineProps<{
  title: string
  hint: string
  active: boolean
}>()

const image = ref<string>('');
const imageName = ref<string>('')
const fileInput = ref<HTMLInputElement | null>(null);

const emit = defineEmits({
  submit: (data) => {
    return true
  },
})

const rules = {
  imageName: { required }
}
const v$ = useVuelidate(rules,{
  imageName
})

const fileComputed = computed(() => {
  if(imageName.value) return imageName.value
  return false
})

const downloadImg = (event: Event) => {
  const target = event.target as HTMLInputElement;
  if(!target.files ) {
    return
  }
  const file = target.files[0];
  const reader = new FileReader();
  reader.onload = (e) => {
    const target = e.target as FileReader;
    image.value = target.result as string;
    imageName.value = file.name;
    const imageData = {
      image: image.value,
      imageName: imageName.value
    };
    emit('submit', imageData);
  };
  reader.readAsDataURL(file);
};

const clearImg = () => {
  image.value= ''
  imageName.value = ''
  if (fileInput.value) {
    fileInput.value.value = '';
  }
  v$.value.$touch()
}

const openFileDialog = () => {
  if(fileInput.value) fileInput.value.click();
}

</script>

<style scoped>

.form {
  position: relative;
  width: 318px;
  display: flex;
  flex-direction: column;
  background-color: #fff;
  border-radius: 6px;
}
.form div {
  display: flex;
  align-items: center;
}
.form-title {
  font-size: 13px;
  font-weight: 500;
  color: #111827;
  padding-bottom: 12px;
}
.form-file-name {
  font-size: 14px;
  font-weight: 400;
  color: #6B7280;
}
.form-file-name-none {
  color: #9CA3B0  ;
}
.form-footer {
  font-size: 13px;
  font-weight: 400;
  padding-top: 8px;
  color: #6B7280;
}

.custom-file-upload {
  height: 40px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: #384252;
  font-size: 14px;
  font-weight: 400;
  cursor: pointer;
  background-color: #fff;
  border: 1px solid #E5E7EB;
  border-radius: 12px;
  box-shadow: 0px 4px 8px 0px rgba(0, 0, 0, 0.02),
              0px 3px 7px -3px rgba(0, 0, 0, 0.08);
  margin-right: 12px;
  padding: 0 12px;
}
.custom-file-upload:hover {
  background-color: #F3F4F6;
  border:1px solid #D0D5DC;
}
.custom-file-upload:active {
  background-color: #E5E7EB;
}
.custom-file-input {
  display: none;
}

.notActive {
  color: #9CA3B0;
  font-size: 14px;
  font-weight: 400;
  border: 1px solid #E5E7EB;
  pointer-events: none;
}
.notActive:hover {
  background-color: #fff;
  border: 1px solid #E5E7EB;
}
.notActive:active {
  background-color: #fff;
}
.error {
  color: #EF4343;
}
</style>