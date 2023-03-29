<script setup lang="ts">
interface OidInfo {
  name: string;
  value: string;
}

const exampleOidInfo: OidInfo[] = [
  {
    name: "Name",
    value: "sysDescr",
  },
  {
    name: "OID",
    value: ".1.3.6.1.2.1.1.1",
  },
  {
    name: "MIB",
    value: "SNMPv2-MIB",
  },
  {
    name: "Syntax",
    value: "DisplayString (OCTET STRING) (SIZE (0..255))",
  },
  {
    name: "Access",
    value: "read-only",
  },
  {
    name: "Status",
    value: "mandatory",
  },
  {
    name: "DefVal",
    value: "",
  },
  {
    name: "Indexes",
    value: "",
  },
  {
    name: "Description",
    value:
      "A textual description of the entity.  This value should include the full name and version identification of the system's hardware type, software operating-system, and networking software.",
  },
];
</script>

<template>
  <div class="scrollbar h-full overflow-y-auto p-2 pb-14">
    <div class="flow-rootoverflow-x-clip">
      <div class="-my-2 -mx-4 sm:-mx-6 lg:-mx-8">
        <div class="inline-block min-w-full py-2 align-middle sm:px-6 lg:px-8">
          <table class="h-full min-w-full divide-y divide-gray-300">
            <tbody class="divide-y divide-gray-200 bg-white">
              <tr
                v-for="info in exampleOidInfo"
                :key="info.name"
                class="divide-x divide-gray-200"
              >
                <td
                  class="whitespace-nowrap py-4 pl-2 pr-4 text-left text-sm font-medium text-gray-900"
                >
                  {{ info.name }}
                </td>
                <td
                  class="whitespace-normal py-4 px-4 text-left text-sm text-gray-900"
                >
                  {{ info.value }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
