<script setup>
import { reactive, toRefs } from 'vue';

// Props definition
defineProps({
  tips: {
    type: [String, Array],
    default: '',
  },
  editTips: {
    type: String,
    default: '',
  },
  authTips: {
    type: String,
    default: '',
  },
  hideTipIcon: {
    type: Boolean,
    default: false,
  },
  size: {
    type: Number,
    default: 12,
  },
  color: {
    type: String,
    default: '#EF7800',
  },
});

// Utility methods for type checking
const methods = {
  getType: (obj) => Object.prototype.toString.call(obj).slice(8, -1),
  isArray: (obj) => Object.prototype.toString.call(obj).slice(8, -1) === 'Array',
};

const state = reactive({});
</script>

<template>
  <div class="bottom-tips">
    {/* Render multiple tips if `tips` is an array */}
    {methods.isArray(tips) ? (
      tips.map((tip) => (
        <div key={tip.id} class="tips">
          {/* Optional icon for each tip */}
          {!hideTipIcon && (
            <rc-icon size={size} color={color} class="tips-icon">
              <RcdExclamationCircleF />
            </rc-icon>
          )}
          <span class="tips__text">{tip.text}</span>
        </div>
      ))
    ) : tips ? (
      {/* Render a single tip if `tips` is a string */}
      <div class="tips">
        {/* Optional icon for the single tip */}
        {!hideTipIcon && (
          <rc-icon size={size} color={color}>
            <RcdExclamationCircleF />
          </rc-icon>
        )}
        <span class="tips__text">{tips}</span>
      </div>
    ) : null}

    {/* Render `editTips` if it exists */}
    {editTips ? (
      <div class="tips">
        {/* Optional icon for edit tips */}
        {!hideTipIcon && (
          <rc-icon size={size} color={color}>
            <RcdExclamationCircleF />
          </rc-icon>
        )}
        <span class="tips__text">{editTips}</span>
      </div>
    ) : authTips ? (
      {/* Render `authTips` if `editTips` does not exist */}
      <div class="tips">
        {/* Optional icon for auth tips */}
        {!hideTipIcon && (
          <rc-icon size={size} color={color}>
            <RcdExclamationCircleF />
          </rc-icon>
        )}
        <span class="tips__text">{authTips}</span>
      </div>
    ) : null}
  </div>
</template>

<style lang="scss" scoped>
@import '@/components/styles/mixin.scss';
@import '@/components/common/theme/admin/variable.scss';

// Define scoped styles for the component
$page-name: 'bottom';

@include b('tips') {
  .tips-icon {
    margin-right: 3px; // Add spacing between the icon and the text
  }
  .tips {
    display: flex; // Align text and icon horizontally
    align-items: center;
    font-size: var(--rcd-font-size-12x);
    color: var(--rcd-color-text-200);
    line-height: var(--rcd-line-height-18);
    margin-top: 8px; // Add spacing between each tip
  }
}
</style>
