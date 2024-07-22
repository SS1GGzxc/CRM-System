<script setup lang="ts">
import { useAuthStore, useIsLoadingStore } from '@/store/auth.store';
import { v4 as uuid } from 'uuid';

useSeoMeta({
  title: "Login | CRM System"
})

const emailRef = ref('')
const passwordRef = ref('')

const isloadingStore = useIsLoadingStore()
const authStore = useAuthStore()
const router = useRouter()

const login = async () => {
  isloadingStore.set(true)
  await account.createEmailPasswordSession(emailRef.value, passwordRef.value)
  const response = await account.get()
  if(response) {
    authStore.set({
      email: response.email,
      name: response.name,
      status: response.status,
    })
  }

  emailRef.value = ''
  passwordRef.value = ''

  await router.push('/')
  isloadingStore.set(false)
}

const register = async () => {
  await account.create(uuid(), emailRef.value, passwordRef.value)
  await login()
}

</script>

<template>
  <div class="flex items-center justify-center min-h-screen w-full">
    <div class="rounded bg-slidebar w-1/4 p-5">
      <h1 class="text-2x1 font-bold text-center mb-5">Login</h1>

      <form>
        <UiInput placeholder="Email" type="email" class="mb-3" v-model="emailRef" />
        <UiInput placeholder="Password" type="password" class="mb-3" v-model="passwordRef" />
        <div class="flex items-center justify-center gap-5">
          <UiButton type="button" @click="login" >Login</UiButton>
          <UiButton type="button" @click="register">Register</UiButton>
        </div>
      </form>
    </div>
  </div>
</template>
