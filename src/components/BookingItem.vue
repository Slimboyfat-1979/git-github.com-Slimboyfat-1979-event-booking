<template>
  <SectionCard>
  <div class="flex justify-between">
    <div class="flex space-x-2">
        <div>{{ title }}</div>
        <div><component :is="icon" :class="{'animate-spin': pending}"></component></div>
    </div>
    <RoundButton @click="$emit('cancelled')" variant="danger">
        Cancel
    </RoundButton>
  </div>
  </SectionCard>
</template>

<script setup>
import { computed } from 'vue';
import RoundButton from './RoundButton.vue';
import SectionCard from './SectionCard.vue';
import { LoaderCircle, Check } from 'lucide-vue-next';


const props = defineProps({
    title: {
        type: String
    },
    status: {
        type: String
    }
});

const pending = computed(() => props.status === 'pending');
const icon = computed(() => pending.value === true ? LoaderCircle : Check)

defineEmits(['cancelled'])


</script>
