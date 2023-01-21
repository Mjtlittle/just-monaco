<script lang="ts">
  import { editor as m_editor } from "monaco-editor";
  import { onMount } from "svelte";

  let editor: m_editor.IStandaloneCodeEditor;
  let editor_element: HTMLDivElement;

  import editorWorker from "monaco-editor/esm/vs/editor/editor.worker?worker";
  // import jsonWorker from "monaco-editor/esm/vs/language/json/json.worker?worker";
  // import cssWorker from "monaco-editor/esm/vs/language/css/css.worker?worker";
  // import htmlWorker from "monaco-editor/esm/vs/language/html/html.worker?worker";
  // import tsWorker from "monaco-editor/esm/vs/language/typescript/ts.worker?worker";

  onMount(() => {
    self.MonacoEnvironment = {
      getWorker: function (_moduleId: any, label: string) {
        // if (label === "json") {
        //   return new jsonWorker();
        // }
        // if (label === "css" || label === "scss" || label === "less") {
        //   return new cssWorker();
        // }
        // if (label === "html" || label === "handlebars" || label === "razor") {
        //   return new htmlWorker();
        // }
        // if (label === "typescript" || label === "javascript") {
        //   return new tsWorker();
        // }
        return new editorWorker();
      },
    };

    editor = m_editor.create(editor_element, {
      value: "",
      language: "plaintext",
      theme: "vs-dark",
      automaticLayout: true,
    });

    return () => {
      editor.dispose();
    };
  });
</script>

<div class="container" bind:this={editor_element} />

<style>
  :global(body) {
    margin: 0;
    overflow: none;
  }

  div {
    height: 100vh;
    width: 100vw;
  }
</style>
