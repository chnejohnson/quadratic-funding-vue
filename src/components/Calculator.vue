<template>
  <div class="flex p-5">
    <div class="w-1/2 border-2 border-gray-100 p-5">
      <p>MATCH AMOUNT</p>
      <input
        v-model="matchAmount"
        type="number"
      />
    </div>
    <div class="w-1/2 border-2 border-gray-100 p-5">
      <p>NUMBER OF PROJECTS</p>
      <p class="text-xl">{{ grantsNum }}</p>
    </div>
  </div>

  <div class="px-5">
    <div class="mx-auto box-border bg-white border-2 border-gray-100 shadow rounded-lg p-5">
      <h1 class="text-3xl">Grants</h1>

      <table class="border-collapse w-full my-5">
        <thead>
          <tr>
            <th class="w-1 p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">Remove</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">Grant</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">Funding</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">Funded Amount</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">Match Amount</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(grant, i) in grants"
            :key="i"
            class="bg-white lg:hover:bg-gray-100 flex lg:table-row flex-row lg:flex-row flex-wrap lg:flex-no-wrap mb-10 lg:mb-0"
          >
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">Remove</span>
              <icon-trash
                class="icon-btn w-6"
                @click="removeGrant(i)"
              />
            </td>
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b text-center block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">Grant</span>
              <div>Grant #{{ i }}</div>
            </td>

            <!-- Funding -->
            <td class="w-full max-w-sm lg:w-auto p-3 text-gray-800 border border-b block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">Funding</span>
              <div>
                <span>
                  <tag />
                  <tag />
                </span>

                <input
                  type="text"
                  class="inline-block w-1/2 appearance-none block bg-white text-gray-700 border border-gray-200 rounded py-2 px-4 leading-tight focus:outline-none focus:ring-1 focus:ring-blue-500 focus:border-blue-500"
                  placeholder="Add a contribution and press Enter"
                />
              </div>
            </td>
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b text-center block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">Funded Amount</span>
            </td>
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b text-center block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">Match Amount</span>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- button -->
      <div class="flex justify-center">
        <button
          class="btn mx-2"
          @click="addGrant"
        >Add Grant</button>
        <button
          class="btn mx-2"
          @click="copyURL"
        >Copy URL</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, computed, defineComponent } from "vue";
import IconTrash from "./IconTrash.vue";
import Tag from "./Tag.vue";

export default defineComponent({
  components: { IconTrash, Tag },
  setup() {
    const matchAmount = ref(0);
    const grants = ref([
      { funding: [], fundingAmount: 0, match: 0 },
      { funding: [], fundingAmount: 0, match: 0 },
      { funding: [], fundingAmount: 0, match: 0 },
      { funding: [], fundingAmount: 0, match: 0 },
    ]);

    const grantsNum = computed(() => grants.value.length);

    const addGrant = () => {
      grants.value.push({ funding: [], fundingAmount: 0, match: 0 });
    };

    const removeGrant = (index: number) => {
      grants.value = grants.value.filter((g, i) => i !== index);
    };

    const copyURL = () => {};

    return { grants, grantsNum, matchAmount, addGrant, copyURL, removeGrant };
  },
});
</script>