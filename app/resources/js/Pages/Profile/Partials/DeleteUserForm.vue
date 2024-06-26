<script setup lang="ts">
import AppButton from '@/Components/App/AppButton.vue';
import AppInput from '@/Components/App/AppInput.vue';
import Modal from '@/Components/Modal.vue';
import { useForm } from '@inertiajs/vue3';
import { nextTick, ref } from 'vue';

const confirmingUserDeletion = ref(false);
const passwordInput = ref<HTMLInputElement | null>(null);

const form = useForm({
    password: '',
});

const confirmUserDeletion = () => {
    confirmingUserDeletion.value = true;

    nextTick(() => passwordInput.value?.focus());
};

const deleteUser = () => {
    form.delete(route('profile.destroy'), {
        preserveScroll: true,
        onSuccess: () => closeModal(),
        onError: () => passwordInput.value?.focus(),
        onFinish: () => {
            form.reset();
        },
    });
};

const closeModal = () => {
    confirmingUserDeletion.value = false;

    form.reset();
};
</script>

<template>
    <section class="space-y-6">
        <header>
            <h2 class="text-lg font-medium">Delete Account</h2>

            <p class="mt-1 text-sm">
                Once your account is deleted, all of its resources and data will be permanently deleted. Before deleting
                your account, please download any data or information that you wish to retain.
            </p>
        </header>

        <AppButton color="danger" @click="confirmUserDeletion">Delete Account</AppButton>

        <Modal :show="confirmingUserDeletion" @close="closeModal">
            <div class="p-6">
                <h2 class="text-lg font-medium">
                    Are you sure you want to delete your account?
                </h2>

                <p class="mt-1 text-sm">
                    Once your account is deleted, all of its resources and data will be permanently deleted. Please
                    enter your password to confirm you would like to permanently delete your account.
                </p>

                <div class="mt-6">
                    <AppInput label="Password" v-model="form.password" v-model:error="form.errors.password" placeholder="password" type="password" @keyup.enter="deleteUser"/>
                </div>

                <div class="flex justify-end gap-2 mt-6">
                    <AppButton color="default" @click="closeModal"> Cancel </AppButton>

                    <AppButton
                        color="danger"
                        :disabled="form.processing"
                        @click="deleteUser"
                    >
                        Delete Account
                    </AppButton>
                </div>
            </div>
        </Modal>
    </section>
</template>
