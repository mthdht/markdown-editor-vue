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
                activePanel: '',
                content: '',
                selectionStart: 0,
                selectionEnd: 0,
                offset: 0
            };
        },
        props: {
            color: {
                type: String,
                default: 'teal',
                validator: function (value) {
                    return ['gray', 'red', 'orange', 'yellow', 'green', 'teal', 'blue', 'indigo', 'purple', 'pink'].indexOf(value) !== -1
                }
            },
            theme: {
                type: String,
                default: 'basic',
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
                return this.theme === 'basic' ? this.color + '-dark' : this.color
            },
            showPanelEdit: function () {
                return this.activePanel === 'preview' ? 'hidden' : ''
            },
            showPanelPreview: function () {
                return this.activePanel === 'edit' ? 'hidden' : ''
            },
            textAreaColor: function () {
                return 'text-' + this.color + '-dark'
            },

        },
        mounted: function () {
            this.activePanel = document.getElementById('markdown-editor').offsetWidth < 768 ? 'edit' : 'doublePanel'
        },
        updated: function () {
            this.getElement().focus()
            this.getElement().setSelectionRange(this.selectionEnd + this.offset, this.selectionEnd + this.offset)
            this.offset = 0
        },
        watch: {
          content: function () {
              this.selectionStart = this.getElement().selectionStart
              this.selectionEnd = this.getElement().selectionEnd
          }
        },
        methods: {
            getElement: function() {
                return document.getElementById('markdown-content')
            },
            getSelectedArea: function() {
                let element = this.getElement()
                return element.value.substring(element.selectionStart,element.selectionEnd)
            },
            applyStyle: function (name) {
                let element = this.getElement()
                this.selectionStart = element.selectionStart
                this.selectionEnd = element.selectionEnd
                switch (name) {
                    case 'bold':
                        this.content = this.content.slice(0,element.selectionStart) + '**' + this.getSelectedArea() + '**' + this.content.slice(element.selectionEnd);
                        this.offset = 2
                        break
                    case 'italic':
                        this.content = this.content.slice(0,element.selectionStart) + '*' + this.getSelectedArea() + '*' + this.content.slice(element.selectionEnd);
                        this.offset = 1
                        break
                    case 'heading':
                        this.content = this.content.slice(0,element.selectionStart) + '# ' + this.getSelectedArea() + this.content.slice(element.selectionEnd);
                        this.offset = (this.selectionStart - this.selectionEnd)
                        break
                    case 'numberedList':
                        this.content = this.content.slice(0,element.selectionStart) + '- ' + this.getSelectedArea() + this.content.slice(element.selectionEnd);
                        this.offset = 2
                        break
                    case 'bulletedList':
                        this.content = this.content.slice(0,element.selectionStart) + '1. ' + this.getSelectedArea() + this.content.slice(element.selectionEnd);
                        this.offset = 2
                        break
                    case 'code':
                        this.content = this.content.slice(0,element.selectionStart) + '``` css\n ' + this.getSelectedArea() + '\n```' + this.content.slice(element.selectionEnd);
                        this.offset = (this.selectionEnd - this.selectionStart) + 7
                        break
                    case 'quote':
                        this.content = this.content.slice(0,element.selectionStart) + '> ' + this.getSelectedArea() + this.content.slice(element.selectionEnd);
                        this.offset = 2
                        break
                    case 'link':
                        this.content = this.content.slice(0,element.selectionStart) + '[' + this.getSelectedArea() +']()' + this.content.slice(element.selectionEnd);
                        this.offset = this.getSelectedArea() ? 3 : 1
                        break
                    case 'image':
                        this.content = this.content.slice(0,element.selectionStart) + '![' + this.getSelectedArea() +']()' + this.content.slice(element.selectionEnd);
                        this.offset = this.getSelectedArea() ? 4 : 2
                        break
                    case 'edit':
                        this.activePanel = 'edit'
                        break
                    case 'preview':
                        this.activePanel = 'preview'
                        break
                    case 'doublePanel':
                        this.activePanel = 'doublePanel'
                }
            }
        }
    }
</script>

<template>
    <div class="wrapper" style="height: 600px">
        <article class="markdown-editor" id="markdown-editor">
            <section class="toolbar" :class="toolbarClass">
                <button v-for="(value, name, index) in buttons"
                        @click="applyStyle(name)"
                        :class="[name === activePanel ? toolbarButtonActive  : '', toolbarButtonClass]"
                        :key="index" class="toolbar-button"
                        :title="value.title">
                    <i class="material-icons">{{ value.class }}</i>
                </button>
            </section>
            <section class="content" id="content">
                <div class="markdown" :class="showPanelEdit">
                    <textarea id="markdown-content" v-model="content" :class="textAreaColor" autofocus placeholder="# Add a heading" style="width: 100%; height: 100%; resize: none;"></textarea>
                </div>
                <div class="preview" :class="showPanelPreview"></div>
            </section>
        </article>
    </div>
</template>

<style scoped>
    @import "~material-design-icons/iconfont/material-icons.css";
    @import "color.css";

    body, html {
        height: 100%;
    }

    * {
        box-sizing: border-box;
    }

    .markdown textarea {
        border: none;
        padding: 10px;
    }

    *:focus {
        outline: none;
    }

    .hidden {
        display: none !important;
    }

    .w-full {
        width: 100%;
    }

    .markdown-editor {
        display: flex;
        flex-flow: column;
        height: 100%;
    }

    .toolbar {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        padding: 5px 10px;

    }

    .toolbar-button {
        margin: 0 6px;
        border: 0;
        padding: 8px;
        display: flex;
        align-items: center;
        border-radius: 7px;

    }

    .content {
        display: flex;
        flex-flow: column;
        align-items: stretch;
        flex-grow: 1;
    }

    .markdown {
        border-bottom: 2px solid darkgray;
        flex-grow: 1;
    }

    .preview {
        border-top: 2px solid darkgray;
        flex-grow: 1;
    }

    @media (min-width: 640px) {

        .toolbar-button {
            padding: 10px;
        }
    }

    @media (min-width: 768px) {
        .md-flex {
            display: flex !important;
        }

        .toolbar {
            padding: 10px 20px;
            justify-content: start;
        }

        .content {
            flex-flow: row;
        }

        .markdown {
            border-right: 2px solid darkgray;
            border-bottom: none;
            width: 50%;
        }

        .preview {
            border-left: 2px solid darkgray;
            border-top: none;
            width: 50%;
        }

        .markdown textarea {
            padding: 20px;
        }
    }
</style>
