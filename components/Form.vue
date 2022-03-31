<template>
  <section class="section-wrapper">
    <div class="content">
      <div class="space-y-10 w-full">
        <div class="max-w-xl mx-auto text-center">
          <h1 class="font-bold text-4xl leading-7">ClinicBank Transfer</h1>
        </div>
        <form @submit.prevent="handleTransfer">
          <div
            class="card px-10 py-10 mx-auto max-w-xl rounded-3xl shadow-lg bg-white"
          >
            <div class="space-y-6 py-4">
              <label class="font-normal mt-10"> Your Wallet Address </label>
              <input
                v-model="form.from"
                aria-label="your address"
                type="text"
                placeholder="Kindly Provide your Wallet Address"
                :class="{ disabled: loading }"
              />
            </div>
            <div class="space-y-6 py-4">
              <label class="font-normal mt-10">
                Destination Wallet Address
              </label>
              <input
                v-model="form.to"
                aria-label="destination address"
                type="text"
                placeholder="Kindly Provide your Destination Wallet Address"
                :class="{ disabled: loading }"
              />
            </div>

            <div class="space-y-6 py-4">
              <label class="font-normal mt-10"> Amount </label>
              <input
                v-model="form.amount"
                aria-label="amount"
                type="text"
                placeholder="ClinicBank Amount"
                :class="{ disabled: loading }"
              />
            </div>

            <div class="button-wrapper">
              <button :disabled="loading" class="btn" type="submit">
                <span v-if="loading">Loading...</span>
                <span v-else>Transfer</span>
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </section>
</template>

<script lang="ts">
import { defineComponent, PropType, reactive, ref } from '@vue/composition-api'
export default defineComponent({
  name: 'Form',
  props: {
    contract: {
      type: Object as PropType<any>,
      default: null,
    },
  },
  setup(props, { root }) {
    interface ClinicForm {
      from: string
      to: string
      amount: string
    }

    interface DisplayMessage {
      type: string
      message: string
    }
    const form = reactive<ClinicForm>({
      from: '',
      to: '',
      amount: '',
    })

    const gas = ref<number>(30000)
    const gasPrice = ref<number>(30000)

    const loading = ref<boolean>(false)
    const message = reactive<DisplayMessage>({
      type: '',
      message: '',
    })
    const handleTransfer = async () => {
      const to = form.to
      const value = form.amount
      loading.value = true
      try {
        await props.contract.methods.transfer(to, value).send({
          from: form.from,
          gas: gas.value,
          gasPrice: gasPrice.value,
        })
        message.type = 'success'
        message.message = 'Transaction Successful'
        // @ts-ignore
        root.$toast.success(message.message)
      } catch (e) {
        message.message = 'something went wrong'
        // @ts-ignore
        root.$toast.error(message.message, { duration: 3000 })
      }
      loading.value = false
    }
    return {
      loading,
      form,
      message,
      handleTransfer,
    }
  },
})
</script>

<style scoped>
.section-wrapper {
  @apply mx-auto mt-20  justify-center w-full;
}
input {
  @apply rounded-lg px-6 py-5 w-full   block;
  @apply border border-[#9DA8B6] focus:ring-1 focus:ring-offset-1 focus:ring-[#005C51];
  @apply relative focus-within:border-[#005C51] focus:border-[#005C51] max-w-2xl;
  @apply focus-within:outline-none focus:outline-none bg-[#f9fafc] transition;
}
/*
 * Buttons: primary
 */

.button-wrapper button {
  @apply rounded-xl px-6 py-4;
  @apply border border-transparent focus:ring-2 focus:ring-offset-1 focus:ring-[#ffce00];
  @apply relative focus-within:border-[#ffce00] focus:border-[#ffce00];
  @apply focus-within:outline-none focus:outline-none bg-[#ffce00] text-white;
}
.btn {
  @apply text-center text-white bg-[#388379] active:bg-[#00574d] hover:bg-[#388379] font-normal px-4 py-2;
  @apply rounded shadow-sm hover:shadow-lg outline-none focus:outline-none ease-linear transition-all duration-150;
  @apply relative border border-transparent focus:ring-2 focus:ring-offset-1 focus:ring-[#388379];
}
</style>
