<script>
  import StarterKit from "@tiptap/starter-kit";
  import { Editor } from "@tiptap/core";
  import Image from "@tiptap/extension-image";

  import { onMount } from "svelte";
  export let editor = null;

  let element = null;

  Image.configure({
    allowBase64: true,
  });

  onMount(() => {
    const content = localStorage.getItem("saved") || "<p>Hello World! 🌍️ </p>";
    console.log("@content", content, localStorage.getItem("saved"));
    editor = new Editor({
      element: element,
      extensions: [Image, StarterKit],
      content,
      onTransaction: () => {
        // force re-render so `editor.isActive` works as expected
        editor = editor;
      },
    });
  });
</script>

<button
  on:click={() => {
    if (!editor) {
      return;
    }
    localStorage.setItem("saved", editor.getHTML());
  }}
>
  SAVE
</button>

<label>
  <input
    type="file"
    on:change={(ev) => {
      const file = ev.target.files[0];
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = () => {
        editor.chain().focus().setImage({ src: reader.result }).run();
      };
    }}
  />
  🖼️</label
>

<div class="p-2" bind:this={element} />
