<script lang="ts" setup>
import { computed, ref } from "vue";
import { Icon } from "@iconify/vue";
import { OidTree } from "../../utils/treeBuilder";
import { EventsEmit, EventsOn } from "../../../wailsjs/runtime/runtime";
import { SendGetRequestWithOid } from "../../../wailsjs/go/main/App";

const showChildren = ref(false);
const isSelected = ref(false);

const props = defineProps<{
  node: OidTree;
  depth: number;
}>();

const indent = computed(() => {
  return { transform: `translate(${props.depth * 20}px)` };
});

function toggleChildren() {
  showChildren.value = !showChildren.value;
}

function cursorClass(): string {
  if (props.node.children !== undefined) {
    return "cursor-pointer";
  } else {
    return "cursor-default";
  }
}

function hasChildren(): boolean {
  return props.node.children !== undefined;
}

function calculatePadding(): string {
  let padding = "pl-1";
  if (!hasChildren()) {
    padding = "pl-6";
  }

  return padding;
}

// TODO : conformance OIDs

function getIconString() {
  const type = props.node.type;
  let retVal = "";

  if (type === "ObjectIdentity" || type === "ModuleIdentity") {
    retVal = "mdi:folder-outline";
  } else if (props.node.is_index) {
    retVal = "mdi:key-variant";
  } else if (props.node.syntax && props.node.syntax.includes("SEQUENCE OF")) {
    retVal = "mdi:table-large";
  } else if (props.node.is_row) {
    retVal = "mdi:table-row";
  } else if (
    props.node.type === "ObjectType" &&
    props.node.access === "read-only"
  ) {
    retVal = "mdi:leaf";
  } else if (
    props.node.type === "ObjectType" &&
    props.node.access === "read-write"
  ) {
    retVal = "mdi:fountain-pen-tip";
  } else if (
    props.node.type === "ObjectType" &&
    props.node.access === "read-create"
  ) {
    retVal = "mdi:plus-circle-outline";
  } else if (props.node.type === "NotificationType") {
    retVal = "mdi:lightning-bolt";
  }

  return retVal;
}

function printType() {
  console.log(props.node);

  toggleChildren();
}

EventsOn("deselectItems", () => {
  if (isSelected.value) {
    isSelected.value = false;
  }
});

EventsOn("sendSelectedOids", () => {
  if (isSelected.value) {
    const oidString = props.node.oid + ".0";
    SendGetRequestWithOid(oidString);
  }
});

function onClick(payload: MouseEvent) {
  if (!payload.ctrlKey) {
    EventsEmit("deselectItems");
  }

  isSelected.value = true;
}
</script>

<template>
  <div>
    <div class="pb-1">
      <div
        :style="indent"
        :class="cursorClass()"
        class="flex text-gray-900"
        @dblclick="printType()"
      >
        <Icon
          v-if="hasChildren()"
          :icon="
            showChildren ? 'mdi:minus-box-outline' : 'mdi:plus-box-outline'
          "
          height="20"
          width="20"
          @click="toggleChildren"
        />
        <div :class="calculatePadding()" class="flex">
          <Icon :icon="getIconString()" height="20" width="20" />
          <p
            class="ml-1 select-none pr-1"
            :class="isSelected ? 'bg-blue-600 text-white' : ''"
            @click="onClick"
          >
            {{ node.name }}
          </p>
        </div>
      </div>
    </div>
    <div v-show="showChildren">
      <TreeMenu
        v-for="oid in node.children"
        :key="oid.oid"
        :node="oid"
        :depth="depth + 1"
      ></TreeMenu>
    </div>
  </div>
</template>
