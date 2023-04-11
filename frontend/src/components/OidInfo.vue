<script setup lang="ts">
import { EventsOn } from "../../wailsjs/runtime/runtime";
import { GetOidByOidNumber } from "../../wailsjs/go/main/App";
import { oidstorage } from "../../wailsjs/go/models";
import { ref, reactive } from "vue";

interface OidInfo {
  Name: string;
  OID: string;
  MIB: string;
  Syntax: string;
  Access: string;
  Status: string;
  DefVal: string;
  Indexes: string;
  Description: string;
}

const exampleOidInfo: OidInfo = {
  Name: "sysDescr",
  OID: ".1.3.6.1.2.1.1.1",
  MIB: "SNMPv2-MIB",
  Syntax: "DisplayString (OCTET STRING) (SIZE (0..255))",
  Access: "read-only",
  Status: "mandatory",
  DefVal: "",
  Indexes: "",
  Description:
    "A textual description of the entity.  This value should include the full name and version identification of the system's hardware type, software operating-system, and networking software.",
};

const emptyOidInfo: OidInfo = {
  Name: "",
  OID: "",
  MIB: "",
  Syntax: "",
  Access: "",
  Status: "",
  DefVal: "",
  Indexes: "",
  Description: "",
};

const oidInfo = ref(emptyOidInfo);

EventsOn("itemSelected", async (...data: unknown[]) => {
  const oid = data[0] as string;

  const oidData = await GetOidByOidNumber(oid);

  setOidData(oidData);
});

function setOidData(oidData: oidstorage.Oid) {
  oidInfo.value.Name = oidData.name;
  oidInfo.value.OID = oidData.oid;
  oidInfo.value.MIB = oidData.mib;
  oidInfo.value.Syntax = oidData.syntax;
  oidInfo.value.Access = oidData.access;
  oidInfo.value.Status = oidData.status;
  oidInfo.value.DefVal = oidData.def_val;
  oidInfo.value.Indexes = addIndexItems(oidData.indexes);
  oidInfo.value.Description = oidData.description;
}

function addIndexItems(indices: string[] | null): string {
  let concatanatedIndices = "";

  if (indices !== null) {
    for (let i = 0; i < indices.length; i++) {
      if (i !== 0) {
        concatanatedIndices += ", ";
      }
      concatanatedIndices += indices.at(i);
    }
  }
  return concatanatedIndices;
}
</script>

<template>
  <div class="scrollbar h-full overflow-y-auto p-2 pb-14">
    <div class="flow-rootoverflow-x-clip">
      <div class="-my-2 -mx-4 sm:-mx-6 lg:-mx-8">
        <div class="inline-block min-w-full py-2 align-middle sm:px-6 lg:px-8">
          <table class="h-full min-w-full divide-y divide-gray-300">
            <tbody class="divide-y divide-gray-200 bg-white">
              <tr
                v-for="(value, name, index) in oidInfo"
                :key="index"
                class="divide-x divide-gray-200"
              >
                <td
                  class="whitespace-nowrap py-4 pl-2 pr-4 text-left text-sm font-medium text-gray-900"
                >
                  {{ name }}
                </td>
                <td
                  class="whitespace-normal py-4 px-4 text-left text-sm text-gray-900"
                >
                  {{ value }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
