<template>
  <div>
    <main>
      <Registration />
    </main>
  </div>
</template>

<script>
import Registration from "@/components/Registration";
export default {
  name: "RegistrationPage",
  layout: "registrationLayout",
  components: {
    Registration,
  },
  data() {
    return {};
  },
};
</script>
