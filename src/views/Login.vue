<template>
  <div class="flex items-center justify-center h-screen px-6 bg-gray-50">
    <div class="w-full max-w-sm p-6 bg-white rounded-md shadow-md">
      <div class="flex items-center justify-center mb-4">
        <span class="text-2xl font-semibold text-gray-700">Microtest Dashboard</span>
      </div>

      <form class="mt-8" @submit.prevent="handleSubmit">
        <label class="block">
          <span class="text-sm text-gray-700 sr-only">Email address</span>
          <input type="email"
                 class="block bg-gray-50 w-full mt-1 rounded-md  border border-gray-300 px-3  py-2 placeholder-gray-500 focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 transition-colors sm:text-sm"
                 placeholder="Email address" v-model="email"/>
        </label>

        <label class="block mt-3">
          <span class="text-sm text-gray-700 sr-only">Password</span>
          <input type="password"
                 class="block bg-gray-50 w-full mt-1 rounded-md border border-gray-300 px-3  py-2 placeholder-gray-500 focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 transition-colors sm:text-sm"
                 placeholder="Password" v-model="password"/>
        </label>

        <div class="flex items-center justify-between mt-4">
          <div>
            <label class="inline-flex items-center">
              <input type="checkbox" class="text-indigo-600 form-checkbox"/>
              <span class="mx-2 text-sm text-gray-600">Remember me</span>
            </label>
          </div>

          <div>
            <a class="block text-sm text-indigo-700 hover:underline" href="#">Forgot your password?</a>
          </div>
        </div>

        <div class="mt-6">
          <button type="submit"
                  class="w-full px-4 py-2 text-sm text-center text-white bg-indigo-600 rounded-md  hover:bg-indigo-500">
            <div v-if="isLoading">
              <svg role="status" class="inline mr-3 w-4 h-4 text-white animate-spin" viewBox="0 0 100 101" fill="none"
                   xmlns="http://www.w3.org/2000/svg">
                <path
                    d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                    fill="#E5E7EB"/>
                <path
                    d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                    fill="currentColor"/>
              </svg>
            </div>
            <div v-else>Login</div>
          </button>
          <div class="mt-7 text-center text-gray-400 text-xs">
            <span>
              Copyright © {{ year }}
              <a href="https://example.com" rel="" target="_blank" title="Codepen aji"
                 class="text-indigo-500 hover:text-indigo-600 ">Microtest</a></span>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<script lang="ts">
import {ref, defineComponent} from 'vue';
import {useRouter} from 'vue-router';
import {ActionTypes} from '@/store/action-types';
import {useStore} from '@/store';
import {useToast} from "vue-toastification";

export default defineComponent({
  name: "Login",
  setup() {
    const email = ref('test@test.com');
    const password = ref('123456');
    const error = ref()
    const isLoading = ref(false)


    const store = useStore()
    const router = useRouter();
    const toast = useToast();

    const handleSubmit = async () => {
      isLoading.value = true;
      try {
        store.dispatch(ActionTypes.LOGIN, {
          email: email.value,
          password: password.value
        })
        isLoading.value = false;
        await router.push('/');
      } catch (err) {
        console.log(err);

        isLoading.value = false;
        switch (err.code) {
          case 'auth/invalid-email':
            toast.error('Invalid email');
            break
          case 'auth/user-not-found':
            toast.error('No account with that email was found');
            break
          case 'auth/wrong-password':
            toast.error('Incorrect password');
            break
          default:
            toast.error('Email or password was incorrect');
            break
        }
      }
    }

    return {
      handleSubmit,
      email,
      password,
      error,
      isLoading
    };
  },
  data: () => ({
    year: new Date().getFullYear(),
  }),
});
</script>