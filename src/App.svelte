<script lang="ts">
  import {
    KeyCode,
    KeyMod,
    languages,
    editor as monacoEditor,
  } from "monaco-editor";
  import { onMount } from "svelte";

  let editor: monacoEditor.IStandaloneCodeEditor;
  let editor_element: HTMLDivElement;

  import editorWorker from "monaco-editor/esm/vs/editor/editor.worker?worker";
  // import jsonWorker from "monaco-editor/esm/vs/language/json/json.worker?worker";
  // import cssWorker from "monaco-editor/esm/vs/language/css/css.worker?worker";
  // import htmlWorker from "monaco-editor/esm/vs/language/html/html.worker?worker";
  // import tsWorker from "monaco-editor/esm/vs/language/typescript/ts.worker?worker";

  const default_text = [
    "🦋 Just Monaco",
    '"I pretty much just took https://microsoft.github.io/monaco-editor/ and stuck it on a static site"',
  ].join("\n");

  const ls_save_key = "monaco.save";

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

    // languages.registerTokensProviderFactory("plaintext", {});

    editor = monacoEditor.create(editor_element, {
      value: localStorage.getItem(ls_save_key) ?? default_text,
      language: "plaintext",
      theme: "vs-dark",
      automaticLayout: true,
      cursorSmoothCaretAnimation: "on",
      fontSize: 20,
      padding: {
        top: 30,
      },
    });

    editor.addCommand(KeyMod.Shift | KeyCode.Enter, () => {
      editor.getAction("editor.action.quickCommand").run();
    });

    editor.addAction({
      label: "Toggle Word Wrap",
      id: "justmonaco.action.togglewordwrap",
      run: function (editor: monacoEditor.ICodeEditor): void | Promise<void> {
        editor.getOption(monacoEditor.EditorOption.wordWrap) === "off"
          ? editor.updateOptions({ wordWrap: "on" })
          : editor.updateOptions({ wordWrap: "off" });
      },
    });

    const m = editor.getModel();

    const save = () => {
      const text = m.getValue();
      localStorage.setItem(ls_save_key, text);
    };

    // auto save
    let save_timer = null;
    editor.onDidChangeModelContent(() => {
      if (save_timer) clearTimeout(save_timer);
      save_timer = setTimeout(() => {
        save();
        save_timer = null;
      }, 300);
    });

    return () => {
      editor.dispose();
    };
  });
</script>

<div class="container" bind:this={editor_element} />

<style lang="scss">
  :global(body) {
    margin: 0;
    overflow: none;
  }

  div {
    height: 100vh;
    width: 100vw;
  }
</style>
