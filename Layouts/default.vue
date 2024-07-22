<script lang="ts" setup>
import {useAuthStore, useIsLoadingStore} from "@/store/auth.store"

const isloadingStore = useIsLoadingStore()
const authStore = useAuthStore()
const router = useRouter()

onMounted(async () => {
    try {
        const user = await account.get()
        if(user) authStore.set(user)
    } catch (err) {
        await router.push('/login')
    } finally {
        isloadingStore.set(false)
    }
})
</script>

<template>
    <LayoutLoader v-if="isloadingStore.isLoading" />
    <section v-else :class="{grid: authStore.isAuth}" style="min-height: 100vh">
        <LayoutSlidebar v-if="authStore.isAuth" />
        <div>
            <slot />
        </div>
    </section>
</template>

<style scoped>
    .grid {
        display: grid;
        grid-template-columns: 1fr 6fr;
    }
</style>
