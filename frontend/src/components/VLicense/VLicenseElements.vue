<template>
  <ul>
    <li
      v-for="element in elementNames"
      :key="element"
      class="mb-2 flex items-center gap-3 text-sm md:mb-4 md:text-base"
    >
      <VIcon
        view-box="0 0 30 30"
        :size="isSmall || isMobile ? 5 : 6"
        :name="`licenses/${element}`"
      />
      <span v-if="elementNames.length > 1" class="sr-only">{{
        element.toUpperCase()
      }}</span>
      <p class="label-regular" :class="{ 'md:description-regular': !isSmall }">
        {{ getLicenseDescription(element) }}
      </p>
    </li>
  </ul>
</template>

<script lang="ts">
import { computed, defineComponent, PropType } from "vue"
import { camel } from "case"

import type { License } from "~/constants/license"
import { useI18n } from "~/composables/use-i18n"
import { useUiStore } from "~/stores/ui"
import { getElements } from "~/utils/license"

import VIcon from "~/components/VIcon/VIcon.vue"

export default defineComponent({
  name: "VLicenseElements",
  components: { VIcon },
  props: {
    /**
     * the slug of the license
     * @values ALL_LICENSES
     */
    license: {
      type: String as PropType<License>,
      required: true,
    },
    /**
     * the size of the icons and text
     */
    size: {
      type: String as PropType<"big" | "small">,
      default: "big",
    },
  },
  setup(props) {
    const i18n = useI18n()
    const elementNames = computed(() =>
      getElements(props.license).filter((icon) => icon !== "cc")
    )

    const isSmall = computed(() => props.size === "small")
    const uiStore = useUiStore()
    const isMobile = computed(() => !uiStore.isDesktopLayout)

    const getLicenseDescription = (element: string) => {
      return i18n.t(`browsePage.licenseDescription.${camel(element)}`)
    }

    return {
      elementNames,
      isSmall,
      isMobile,

      getLicenseDescription,
    }
  },
})
</script>
