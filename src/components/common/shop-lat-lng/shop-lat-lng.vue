<script setup>
import { reactive, toRefs, onMounted, ref, watch, onBeforeMount } from 'vue';

// Define props
defineProps({
  address: { type: String },
  center: {
    type: Object,
    default: () => ({}),
  },
  showLoading: {
    type: Boolean,
    default: false,
  },
});

// Define emitted events
defineEmits(['input', 'on-change']);

// Reactive state
const state = reactive({
  key: Date.now(), // Unique key for re-rendering
  iframeSrc: '', // Iframe source URL
  latLngData: {
    lat: null,
    lng: null,
  },
  myMapRef: null, // Reference to the iframe
  iframeWin: null, // Reference to the iframe's window
  isloading: false, // Loading state
});

let iframeWin = ''; // To hold iframe window reference

// Watch for changes in the `address` prop
watch(
  () => props.address,
  (newVal, oldVal) => {
    if (newVal !== oldVal) {
      sendMessage({
        key: 'address',
        value: props.address,
      });
    }
  }
);

// Lifecycle hook: runs when the component is mounted
onMounted(() => {
  state.isloading = true; // Set loading state to true
  iframeWin = state.myMapRef?.contentWindow; // Access iframe window
  state.iframeSrc = getIframeSrc(); // Get the iframe source URL
  window.addEventListener('message', getMessage); // Listen for messages from iframe
});

// Lifecycle hook: runs before the component is unmounted
onBeforeMount(() => {
  window.removeEventListener('message', getMessage); // Remove event listener
});

// Get iframe source based on environment
const getIframeSrc = () => {
  const VUE_APP_ENV = process.env.VUE_APP_ENV || process.env.VUE_APP_CUSTOM_ENV;
  if (VUE_APP_ENV === 'development') {
    return `xxx?${Date.now()}`;
  } else if (VUE_APP_ENV === 'pre') {
    return `xxx?${Date.now()}`;
  }
  return `xxx?${Date.now()}`;
};

// Handle messages received from the iframe
const getMessage = ({ data }) => {
  switch (data.key) {
    case 'loading': // Map initialization complete
      state.isloading = false;
      initLatLng(); // Initialize lat-lng
      break;
    case 'latLng': // Coordinate data
      emit('on-change', data.value); // Emit the coordinate data
      break;
    default:
      break;
  }
};

// Send messages to the iframe
const sendMessage = ({ key, value }) => {
  iframeWin?.postMessage({ key, value }, '*');
};

// Initialize lat-lng based on props or fallback to a default location
const initLatLng = () => {
  if (props.center?.lat) {
    const { lat, lng } = props.center;
    sendMessage({
      key: 'latLng',
      value: { lat, lng },
    });
  } else if (props.address) {
    sendMessage({
      key: 'address',
      value: props.address,
    });
  } else {
    // Default to Beijing Tiananmen Square
    sendMessage({
      key: 'latLng',
      value: {
        lat: 39.908821,
        lng: 116.397469,
      },
    });
  }
};
</script>

<template>
  <div class="shopLatLng" v-loading="props.showLoading && state.isloading">
    {/* Render the iframe with dynamic source */}
    <iframe :src="state.iframeSrc" ref={(el) => (state.myMapRef = el)}></iframe>
  </div>
</template>

<style lang="scss" scoped>
.shopLatLng {
  width: 300px;
  height: 190px;
  margin-bottom: 10px;
  
  iframe {
    border: 0;
    width: 100%;
    height: 100%;
  }
}
</style>
