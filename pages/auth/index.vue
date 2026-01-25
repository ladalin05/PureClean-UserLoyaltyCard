<template>
  <div class="mt-8">
    <AuthLogin/>
  </div>
</template>

<script setup>
import {userAuth} from "~/store/userAuth";
import {useRouter} from 'vue-router' // 👈 Import router!
import {onMounted} from "vue";

const useUserAuth = userAuth();
const router = useRouter(); // 👈 Create router instance

onMounted(() => {
  useUserAuth.initializeSession();
  if (useUserAuth.isLoggedIn) {
    router.push('/'); // 👈 Redirect to Home if already logged in
  }
});

</script>