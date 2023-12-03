<script>
    import { onMount } from 'svelte';
    import { writable, derived } from 'svelte/store';
  
    export let texts = [''];
    export let typingSpeed = 200; // Typing speed in milliseconds per character
    export let pauseDuration = 2000; // Pause duration in milliseconds
    export let shuffle = true;
    export let inputPlaceholderId = 'typewriter-input';
  
    const currentIndex = writable(0);
    const currentText = derived(currentIndex, $currentIndex => texts[$currentIndex]);
    let currentCharIndex = 0;
    let inputElement;
    let isTyping = true;
    let timeout;
  
    function updateText() {
      if (isTyping) {
        if (currentCharIndex < $currentText.length) {
          inputElement.placeholder += $currentText.charAt(currentCharIndex);
          currentCharIndex++;
          timeout = setTimeout(updateText, typingSpeed);
        } else {
          timeout = setTimeout(() => {
            isTyping = false;
            currentCharIndex = $currentText.length;
            updateText();
          }, pauseDuration);
        }
      } else {
        if (currentCharIndex > 0) {
          inputElement.placeholder = $currentText.substring(0, --currentCharIndex);
          timeout = setTimeout(updateText, typingSpeed / 2); // Erasing at double speed
        } else {
          if (shuffle) {
            currentIndex.set(Math.floor(Math.random() * texts.length));
          } else {
            currentIndex.update(n => (n + 1) % texts.length);
          }
          isTyping = true;
          updateText(); // Start typing immediately without pause
        }
      }
    }
  
    onMount(() => {
      inputElement = document.getElementById(inputPlaceholderId);
      if (!inputElement) {
        console.error('No input element found with ID:', inputPlaceholderId);
        return;
      }
      updateText();
      return () => clearTimeout(timeout);
    });
  </script>
  