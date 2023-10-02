<script lang="ts">
  import { invoke } from "@tauri-apps/api";
  import { onMount } from "svelte";

  import peter from "$lib/peter.png"

  let recognition;
  let count = 0;

  let griffins = ["Peter", "Meg", "Lois", "Stewie", "Brian", "Chris"];
  let symbols = /[,.]/i;

  onMount(() => {
    const SpeechRecognition =
      window.SpeechRecognition || window.webkitSpeechRecognition;
    recognition = new SpeechRecognition();
    recognition.continuous = true;
    recognition.onresult = function (event) {
      let transcript: string = event.results[count][0].transcript;
      count++;
      let strings = transcript.split(" ");
      for (let i = 0; i < strings.length; i++) {
        const element = strings[i].replace(symbols, "");
        if (griffins.includes(element)) {
          shutdown();
        }
      }
    };
    recognition.start();
  });

  async function shutdown() {
    await invoke("js_shutdown");
  }
</script>

<svelte:head>
  <title>TableTalk</title>
</svelte:head>

<div class="flex flex-row justify-center">
  <img src="{peter}">
</div>
