<script setup lang="ts">
import type { ICustomer } from '@/types/deals.types';
import { useMutation, useQuery } from '@tanstack/vue-query';
import { COLLECTION_CUSTOMERS, DB_ID, STORAGE_ID } from '@/app.constants';
import { v4 as uuid } from 'uuid';

useSeoMeta({
  title: `Edit company | CRM System`
})

interface InputFileEvent extends Event {
  target: HTMLInputElement
}

interface ICustomersFromState extends Pick<ICustomer, 'avatar_url' | 'email' | 'name' | 'from_source'> {}

const route = useRoute()
const customerId = route.params.id as string

const { handleSubmit, defineField, setFieldValue, setValues, values } = useForm<ICustomersFromState>()

const { data, isSuccess } = useQuery({
  queryKey: ['get customer', customerId],
  queryFn: () => DB.getDocument(DB_ID, COLLECTION_CUSTOMERS, customerId),
})

watch(isSuccess, () => {
  const initialData = data.value as unknown as ICustomersFromState
  setValues({
    email: initialData.email,
    avatar_url: initialData.avatar_url,
    from_source: initialData.from_source || '',
    name: initialData.name,
  })
})

const [name, nameAttrs] = defineField('name')
const [email, emailAttrs] = defineField('email')
const [fromSource, fromSourceAttrs] = defineField('from_source')

const { mutate, isPending } = useMutation({
  mutationKey: ['update customer', customerId],
  mutationFn: (data: ICustomersFromState) => DB.updateDocument(DB_ID, COLLECTION_CUSTOMERS, customerId, data),
})

const onSubmit = handleSubmit(data => {
  mutate(data)
})

const {mutate: uploadImage, isPending: isUploadImagePeding} = useMutation({
  mutationKey: ['upload image', customerId],
  mutationFn: (file: File) => storage.createFile(STORAGE_ID, uuid(), file),
  onSuccess(data) {
    const response = storage.getFileDownload(STORAGE_ID, data.$id)
    setFieldValue('avatar_url', response.href)
  }
})

</script>

<template>
  <div class="p-10">
    <h1 class="font-bold text-2x1 mb-10">
      Редактирование {{ (data as unknown as ICustomersFromState)?.name }}
    </h1>

    <form @submit="onSubmit" class="form">
      <UiInput v-model="name" v-bind="nameAttrs" class="input" type="text" placeholder="Наименование" />
      <UiInput v-model="email" v-bind="emailAttrs" class="input" type="email" placeholder="Email" />
      <UiInput v-model="fromSource" v-bind="fromSourceAttrs" class="input" type="text" placeholder="Откуда пришел?" />
      <img v-if="values.avatar_url || isUploadImagePeding" :src="values.avatar_url" alt="" width="65" height="65" class="rounded-full my-4" />
      <div class="grid w-full max-w-sm items-center gap-1.5 input">
        <label>
          <div class="text-sm mb-2">Логотип</div>
          <UiInput type="file" :disabled="isUploadImagePeding" :onchange="(e:InputFileEvent) => e?.target?.files?.length && uploadImage(e.target.files[0])"/>
        </label>
      </div>
      <UiButton :disabled="isPending" variant="secondary" class="mt-3">{{ isPending ? "Загрузка..." : "Сохранить" }}</UiButton>
    </form>
  </div>
</template>

<style scoped>
.input {
  @apply border-[#161c26] mb-4 placeholder:text-[#748092] focus:border-border transition-colors;
}
</style>
