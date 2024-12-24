<script setup lang="ts">
import './shop-copy.scss';
// import { RcMessage } from '@rcd-pro/vue3';
// import locale from './locale';
// import { useI18n } from 'vue-i18n';
// import { RcdCopy } from '@rcd-pro/icons-vue';

// Define props with TypeScript interface and default values
interface IProps {
  text: string;
  copyText?: string;
}
const props = withDefaults(defineProps<IProps>(), {
  text: '',
});

// Internationalization setup
// const { t } = useI18n({ messages: locale });

// Copy text to clipboard and show success message
const handleCopy = () => {
  if (navigator.clipboard) {
    // Use Clipboard API to copy the text
    navigator.clipboard.writeText(props.copyText || props.text);
  } else {
    // Fallback for browsers that don't support Clipboard API
    const textarea = document.createElement('textarea');
    document.body.appendChild(textarea);

    // Hide the textarea
    textarea.style.position = 'fixed';
    textarea.style.clipPath = 'inset(0 0 0 0)';
    textarea.style.top = '10px';

    // Assign value to the textarea
    textarea.value = props.copyText || props.text;

    // Select the text and execute the copy command
    textarea.select();
    document.execCommand('copy', false);

    // Remove the textarea from the DOM
    document.body.removeChild(textarea);
  }

  // Show a success message
//   RcMessage({
//     message: t('copySuccess'),
//     type: 'success',
//     duration: 3000,
//   });
};
</script>

<template>
  <div class="shop-copy">
    {/* Render the main text */}
    {props.text}{' '}
    {/* Render the copy icon with a click event */}
    <span onClick={handleCopy} class="icon">
      <slot name="icon">
        <rc-icon>
          <RcdCopy />
        </rc-icon>
      </slot>
    </span>
  </div>
</template>
