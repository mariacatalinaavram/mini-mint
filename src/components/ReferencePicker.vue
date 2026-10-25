<template>
  <div class="reference-picker">
    <template v-if="options.length">
      <div v-for="option in options" :key="option.step.id">
        <span v-if="option.outputs.length">{{ option.step.displayName }}: </span>
        <catnip-button
          v-for="output in option.outputs"
          :key="output.key"
          size="s"
          appearance="secondary"
          class="reference-picker-output"
          @click="$emit('select', makeReference(option.step.id, output.key))"
        >
          {{ output.label }}
        </catnip-button>
      </div>
    </template>
    <p v-else>No outputs available</p>
  </div>
</template>

<script setup>
import { computed } from "vue";
import { useEditorStore } from "@/store/editor.js";
import { collectGuaranteedPredecessors } from "@/logic/graph.js";
import { getStepOutputs } from "@/logic/stepTypes.js";
import { makeReference } from "@/logic/references.js";

const props = defineProps({
  /** The step that will receive the reference. */
  stepId: { type: String, required: true }
});

/** Emit `select` with a reference string, e.g. makeReference(stepId, outputKey) from logic/references.js */
defineEmits(["select"]);

const editor = useEditorStore();
const options = computed(() => {
  if (!editor.flow) return [];

  return collectGuaranteedPredecessors(editor.flow, props.stepId)
    .map((step) => ({ step, outputs: getStepOutputs(step) }))
    .filter(Boolean)
});
</script>

<style scoped lang="scss">
.reference-picker {
  margin-top: var(--catnip-space-2);
  background: var(--catnip-color-background-neutral-subtle);
  padding: var(--catnip-space-2);
  border-radius: var(--catnip-radius-m);

  &-output {
    margin: var(--catnip-space-1) var(--catnip-space-1) 0 0;
  }
}
</style>
