<template>
  <section class="section-wrapper">
    <div v-if="contractLoaded">
      <clinic-form :contract="contract" />
    </div>
  </section>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from '@vue/composition-api'
import Web3 from 'web3'
import ClinicForm from '~/components/Form.vue'
import abi from '~/abi/clinicbank.json'
export default defineComponent({
  name: 'IndexPage',
  components: { ClinicForm },
  setup() {
    const contractLoaded = ref<boolean>(false)
    const web3 = ref<any>(null)
    const contract = ref<any>(null)
    const contractAddress = ref<string>(
      '0x86C12A724340f3F4F6142789808874d0A55Bd01f'
    )

    const loadContract = async () => {
      contractLoaded.value = true
      try {
        contract.value = await new web3.value.eth.Contract(
          abi,
          contractAddress.value
        )
      } catch (e) {
        contractLoaded.value = false
      }
    }

    onMounted(async () => {
      web3.value = new Web3(Web3.givenProvider)
      // @ts-ignore
      await window.ethereum.enable()
      await loadContract()
    })

    return {
      loadContract,
      web3,
      contract,
      contractLoaded,
      contractAddress,
    }
  },
})
</script>

<style>
.section-wrapper {
  @apply pt-10 pb-10 bg-[#005C51] h-screen w-screen;
}
.content {
  @apply max-w-screen-2xl mx-auto px-6 lg:px-8;
}
</style>
