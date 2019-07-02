<script>
    export default {
        name: 'MarkdownEditor', // vue component name
        data() {
            return {
                buttons: {
                    bold: {
                        title: 'Bold',
                        class: 'format_bold',
                    },
                    italic: {
                        title: 'Italic',
                        class: 'format_italic'
                    },
                    heading: {
                        title: 'Heading',
                        class: 'title'
                    },
                    numberedList: {
                        title: 'Numbered list',
                        class: 'format_list_numbered'
                    },
                    bulletedList: {
                        title: 'Bulleted list',
                        class: 'format_list_bulleted'
                    },
                    code: {
                        title: 'Code block',
                        class: 'code'
                    },
                    quote: {
                        title: 'Quote',
                        class: 'format_quote'
                    },
                    link: {
                        title: 'Insert link',
                        class: 'insert_link'
                    },
                    image: {
                        title: 'Insert Image',
                        class: 'insert_photo'
                    },
                    edit: {
                        title: 'Edit mode',
                        class: 'edit'
                    },
                    doublePanel: {
                        title: 'Side by side mode',
                        class: 'chrome_reader_mode'
                    },
                    preview: {
                        title: 'Preview mode',
                        class: 'remove_red_eye'
                    },
                },
                activePanel: 'doublePanel'
            };
        },
        props: {
            color: {
                type: String,
                default: 'blue',
                validator: function (value) {
                    return ['gray', 'red', 'orange', 'yellow', 'green', 'teal', 'blue', 'indigo', 'purple', 'pink'].indexOf(value) !== -1
                }
            },
            theme: {
                type: String,
                default: 'dark',
                validator: function (value) {
                    return ['dark', 'light', 'basic'].indexOf(value) !== -1
                }
            }
        },
        computed: {
            toolbarClass: function () {
                return this.theme === 'dark' ? this.color + '-dark' : this.theme === 'light' ? this.color + '-light' : this.color
            },
            toolbarButtonClass: function () {
                switch (this.theme) {
                    case 'dark':
                        return this.color + '-dark hover:' + this.color
                    case 'light':
                        return this.color + '-light hover:' + this.color
                    default:
                        return this.color + ' hover:' + this.color + '-dark'
                }
            },
            toolbarButtonActive: function () {
                return this.theme == 'dark' || this.theme == 'light' ? this.color : this.color + '-dark'
            }
        }
    }
</script>

<template>
    <article class="markdown-editor">
        <section class="toolbar" :class="toolbarClass">
            <button v-for="(value, name, index) in buttons" :class="[name === activePanel ? toolbarButtonActive : '', toolbarButtonClass]" :key="index" class="toolbar-button">
                <i class="material-icons">{{ value.class }}</i>
            </button>
        </section>
        <section class="content">
            <div class="markdown">
                <textarea name="" id="" autofocus placeholder="texte"></textarea>
            </div>
            <div class="preview">

            </div>
        </section>
    </article>
</template>

<style scoped>
    @import "~material-design-icons/iconfont/material-icons.css";
    @import "color.css";

    button:focus {
        outline: none;
    }

    .markdown-editor {
        border: 1px solid red;
    }

    .toolbar {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
    }

    .toolbar-button {
        margin: 0 10px;
        border: 0;
        padding: 5px;
        display: flex;
        align-items: center;
        border-radius: 7px;

    }

    @media (min-width: 640px) {

        .toolbar-button {
            padding: 10px;
        }
    }

    @media (min-width: 768px) {
        .toolbar {
            padding: 10px 20px;
            justify-content: start;
        }
    }

    @media (min-width: 1024px) {

    }

    @media (min-width: 1248px) {

    }
</style>
