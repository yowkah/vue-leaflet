<script lang="ts">
import type L from "leaflet";
import {
  defineComponent,
  inject,
  markRaw,
  nextTick,
  onMounted,
  ref,
} from "vue";

import { render } from "@src/functions/layer";
import { setupSvgOverlay, svgOverlayProps } from "@src/functions/svgOverlay";
import {
  AddLayerInjection,
  UseGlobalLeafletInjection,
} from "@src/types/injectionKeys";
import {
  WINDOW_OR_GLOBAL,
  assertInject,
  propsBinder,
  remapEvents,
} from "@src/utils.js";

/**
 * SvgOverlay component, render a svg instead of a geospatial map.
 */
export default defineComponent({
  name: "LSVGOverlay",
  props: svgOverlayProps,
  setup(props, context) {
    const leafletObject = ref<L.SVGOverlay>();
    const ready = ref(false);

    const useGlobalLeaflet = inject(UseGlobalLeafletInjection);
    const addLayer = assertInject(AddLayerInjection);

    const { options, methods } = setupSvgOverlay(props, leafletObject, context);

    onMounted(async () => {
      const { svgOverlay }: typeof L = useGlobalLeaflet
        ? WINDOW_OR_GLOBAL.L
        : await import("leaflet/dist/leaflet-src.esm");
      leafletObject.value = markRaw<L.SVGOverlay>(
        svgOverlay(props.svgImage, props.bounds, options)
      );

      const { listeners } = remapEvents(context.attrs);
      leafletObject.value.on(listeners);
      propsBinder(methods, leafletObject.value, props);
      addLayer({
        ...props,
        ...methods,
        leafletObject: leafletObject.value,
      });
      ready.value = true;
      nextTick(() => context.emit("ready", leafletObject.value));
    });

    return { ready, leafletObject };
  },
  render() {
    return render(this.ready, this.$slots);
  },
});
</script>
