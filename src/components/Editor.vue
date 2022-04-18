<template>
    <div id="editor" style="width: 500px; height: 500px"></div>
</template>

<script>
import loader from "@monaco-editor/loader"

export default {
    name: "Editor",
    data() {
        return {
            content: '',
        }
    },
    props: [
        'language',
        'theme',
        'modelValue',
    ],
    async mounted() {

        loader.init().then((monaco) => {
            const editorOptions = {
                value: this.modelValue,
                language: this.language,
                minimap: { enabled: false },
                theme: this.theme
            }

            var editor = monaco.editor.create(document.getElementById("editor"),   editorOptions)

            editor.onDidChangeModelContent((e) => {
                this.content = editor.getValue()
                this.$emit('update:modelValue', this.content)
            })
        
        })
        
    },
}
</script>