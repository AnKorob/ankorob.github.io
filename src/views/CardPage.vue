<template>
  <div class="card-page" v-loading="isLoading">
    <template v-if="!isLoading">
      <EmailForm
        v-if="isOpenSendEmailBlock"
        @mailsent="isOpenSendEmailBlock = false"
        @close="isOpenSendEmailBlock = false"
      />
      <template v-else>
        <CompanyInfo @open-email-block="isOpenSendEmailBlock = true" />
        <ContractTemplate />
      </template>
    </template>
  </div>
</template>
<script>
import CompanyInfo from "@/components/card-page/company-info/CompanyInfo.vue";
import ContractTemplate from "@/components/card-page/contract-template/ContractTemplate.vue";
import EmailForm from "@/components/card-page/EmailForm.vue";
import { mapState, mapActions } from "vuex";

export default {
  name: "CardPage",
  components: {
    CompanyInfo,
    ContractTemplate,
    EmailForm,
  },
  data: () => ({
    isOpenSendEmailBlock: "",
    isLoading: false,
  }),
  computed: {
    ...mapState(["clientCompany"]),
  },
  watch: {
    "$route.params": {
      handler: function () {
        this.getCompanyByHash();
      },
      deep: true,
      immediate: true,
    },
  },
  methods: {
    ...mapActions(["fetchCompanyByHash"]),
    getCompanyByHash() {
      if (this.clientCompany?.hash !== this.$route.params?.hash) {
        this.isLoading = true;
        this.fetchCompanyByHash(this.$route.params.hash)
          .then(() => (this.isLoading = false))
          .catch((e) => {
            this.$router.push("/");
            this.$message({
              message: `Что-то пошло не так, попробуйте еще раз (${e})`,
              type: "error",
            });
          });
      }
    },
  },
};
</script>
<style lang="scss">
.card-page {
  position: relative;
  min-height: 500px;
  height: 100%;
  width: 100%;
  display: flex;
  padding-bottom: 10px;
  flex-direction: column;
  font-family: "Roboto", sans-serif;
}

@media (max-width: 770px) {
}
</style>
