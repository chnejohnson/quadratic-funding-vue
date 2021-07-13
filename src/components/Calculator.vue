<template>
  <div class="flex p-5">
    <div class="w-1/2 border-2 border-gray-100 p-5 mr-2">
      <p>總配對基金</p>
      <input
        class="text-xl appearance-none block w-4/5 bg-white text-gray-700 border border-gray-200 rounded p-1 leading-tight focus:outline-none"
        v-model="totalMatch"
        type="number"
      />
    </div>
    <div class="w-1/2 border-2 border-gray-100 p-5 ml-2">
      <p>專案數量</p>
      <p class="text-xl">{{ grantsNum }}</p>
    </div>
  </div>

  <div class="px-5">
    <div class="mx-auto box-border bg-white border-2 border-gray-100 shadow rounded-lg p-5">
      <h1 class="text-3xl">專案</h1>

      <table class="border-collapse w-full my-5">
        <thead>
          <tr>
            <th class="w-1 p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">刪除</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">專案</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">捐款</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">捐款總額</th>
            <th class="p-3 font-bold uppercase bg-gray-200 text-gray-600 border border-gray-300 hidden lg:table-cell">配對基金</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(grant, i) in grants"
            :key="i"
            class="bg-white lg:hover:bg-gray-100 flex lg:table-row flex-row lg:flex-row flex-wrap lg:flex-no-wrap mb-10 lg:mb-0"
          >
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">刪除</span>
              <icon-trash
                class="icon-btn w-6"
                @click="removeGrant(i)"
              />
            </td>
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b text-center block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">專案</span>
              <div>專案 #{{ i }}</div>
            </td>

            <!-- Funding -->
            <td class="w-full lg:w-auto p-3 pt-6 lg:pt-3 text-gray-800 border border-b block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">捐款</span>
              <div>
                <span
                  v-for="(fund,j) in grant.funding"
                  :key="j"
                >
                  <tag
                    :amount="fund"
                    @remove="removeFunding(i, j)"
                  />
                </span>

                <input
                  v-model="fundingInputs[i]"
                  @keyup.enter="addFunding(i, fundingInputs[i])"
                  type="text"
                  class="inline-block my-2 appearance-none block bg-white text-gray-700 border border-gray-200 rounded py-2 px-4 leading-tight focus:outline-none focus:ring-1 focus:ring-blue-500 focus:border-blue-500"
                  placeholder="按 Enter 新增捐款"
                />
              </div>
            </td>
            <!-- Funded Amount -->
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b text-center block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">捐款總額</span>
              <p>$ {{ grant.fundingAmount }}</p>
            </td>

            <!-- Match Amount -->
            <td class="w-full lg:w-auto p-3 text-gray-800 text-center border border-b text-center block lg:table-cell relative lg:static">
              <span class="lg:hidden absolute top-0 left-0 bg-blue-200 px-2 py-1 text-xs font-bold uppercase">配對數量</span>
              <p>$ {{ grant.match.toFixed(2) }}</p>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- button -->
      <div class="flex justify-center">
        <button
          class="btn mx-2"
          @click="addGrant"
        >新增專案</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, computed, defineComponent, watch } from "vue";
import IconTrash from "./IconTrash.vue";
import Tag from "./Tag.vue";

interface Grant {
  funding: number[];
  fundingAmount: number;
  match: number;
}

export default defineComponent({
  components: { IconTrash, Tag },
  setup() {
    const totalMatch = ref(1000);
    const grants = ref<Grant[]>([
      { funding: [1, 1, 1], fundingAmount: 0, match: 0 },
      { funding: [9], fundingAmount: 0, match: 0 },
      { funding: [], fundingAmount: 0, match: 0 },
    ]);
    const fundingInputs = ref(["", "", "", ""]);
    const grantsNum = computed(() => grants.value.length);

    const calculateMatch = () => {
      let sum = 0;
      grants.value.forEach((grant) => {
        let rootSum = 0;
        let fundingAmount = 0;
        grant.funding.forEach((fund) => {
          fundingAmount += fund;
          rootSum += Math.sqrt(fund);
        });
        grant.fundingAmount = fundingAmount;
        grant.match = rootSum * rootSum;
        sum += grant.match;
      });

      grants.value.forEach((grant) => {
        grant.match *= totalMatch.value / sum;
      });
    };

    calculateMatch();

    watch(totalMatch, () => {
      calculateMatch();
    });

    const removeFunding = (grantIndex: number, fundingIndex: number) => {
      grants.value[grantIndex].funding = grants.value[
        grantIndex
      ].funding.filter((_, i) => i !== fundingIndex);
      calculateMatch();
    };

    const addFunding = (grantIndex: number, fundingInput: string) => {
      if (!Number(fundingInput)) {
        fundingInputs.value[grantIndex] = "";
        return;
      }
      grants.value[grantIndex].funding.push(Number(fundingInput));
      fundingInputs.value[grantIndex] = "";
      calculateMatch();
    };

    const addGrant = () => {
      grants.value.push({
        funding: [],
        fundingAmount: 0,
        match: 0,
      });
      calculateMatch();
    };

    const removeGrant = (grantIndex: number) => {
      grants.value = grants.value.filter((g, i) => i !== grantIndex);
      calculateMatch();
    };

    return {
      grants,
      grantsNum,
      totalMatch,
      fundingInputs,
      addGrant,
      removeGrant,
      addFunding,
      removeFunding,
      calculateMatch,
    };
  },
});
</script>