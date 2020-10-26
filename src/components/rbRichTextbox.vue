<template>
  <div>
    <textarea :id="id" v-model="content"></textarea>
  </div>
</template>
<script>
import tinymce from "tinymce/tinymce";
import "tinymce/icons/default";
import "tinymce/themes/silver";
import "tinymce/skins/ui/oxide/skin.min.css";
// Any plugins you want to use has to be imported
import "tinymce/plugins/advlist";
import "tinymce/plugins/wordcount";
import "tinymce/plugins/autolink";
import "tinymce/plugins/autosave";
import "tinymce/plugins/charmap";
import "tinymce/plugins/codesample";
import "tinymce/plugins/contextmenu";
import "tinymce/plugins/emoticons";
import "tinymce/plugins/fullscreen";
import "tinymce/plugins/hr";
import "tinymce/plugins/imagetools";
import "tinymce/plugins/insertdatetime";
import "tinymce/plugins/link";
import "tinymce/plugins/media";
import "tinymce/plugins/noneditable";
import "tinymce/plugins/paste";
import "tinymce/plugins/print";
import "tinymce/plugins/searchreplace";
import "tinymce/plugins/tabfocus";
import "tinymce/plugins/template";
import "tinymce/plugins/textpattern";
import "tinymce/plugins/visualblocks";
import "tinymce/plugins/anchor";
import "tinymce/plugins/autoresize";
import "tinymce/plugins/bbcode";
import "tinymce/plugins/code";
import "tinymce/plugins/colorpicker";
import "tinymce/plugins/directionality";
import "tinymce/plugins/fullpage";
import "tinymce/plugins/help";
import "tinymce/plugins/image";
import "tinymce/plugins/importcss";
import "tinymce/plugins/legacyoutput";
import "tinymce/plugins/lists";
import "tinymce/plugins/nonbreaking";
import "tinymce/plugins/pagebreak";
import "tinymce/plugins/preview";
import "tinymce/plugins/save";
import "tinymce/plugins/spellchecker";
import "tinymce/plugins/table";
import "tinymce/plugins/textcolor";
import "tinymce/plugins/toc";
import "tinymce/plugins/visualchars";

export default {
  data() {
    return {
      content: this.value,
      editor: null,
      cTinyMce: null,
      checkerTimeout: null,
      isTyping: false,
    };
  },
  name: "rbRichTextbox",
  methods: {
    init() {
      let options = {
        selector: "#" + this.id,
        skin: false,
        toolbar: this.toolbar,
        plugins: this.plugins,
        init_instance_callback: this.initEditor,
      };
      tinymce.init(this.concatAssciativeArrays(options, this.options));
    },
    initEditor(editor) {
      this.editor = editor;
      editor.on("KeyUp", (e) => {
        this.submitNewContent();
      });
      editor.on("Change", (e) => {
        if (this.editor.getContent() !== this.value) {
          this.submitNewContent();
        }
        this.$emit("editor-change", e);
      });
      editor.on("init", (e) => {
        editor.setContent(this.content);
        this.$emit("input", this.content);
      });
      if (this.readonly) {
        this.editor.setMode("readonly");
      } else {
        this.editor.setMode("design");
      }

      this.$emit("editor-init", editor);
    },
    concatAssciativeArrays(array1, array2) {
      if (array2.length === 0) return array1;
      if (array1.length === 0) return array2;
      let dest = [];
      for (let key in array1) dest[key] = array1[key];
      for (let key in array2) dest[key] = array2[key];
      return dest;
    },
    submitNewContent() {
      this.isTyping = true;
      if (this.checkerTimeout !== null) clearTimeout(this.checkerTimeout);
      this.checkerTimeout = setTimeout(() => {
        this.isTyping = false;
      }, 300);

      this.$emit("input", this.editor.getContent());
    },
  },
  props: {
    id: {
      type: String,
      required: true,
    },
    value: {
      default() {
        return "";
      },
    },
    htmlClass: {
      default: "",
      type: String,
    },
    plugins: {
      default: function () {
        return [
          "advlist autolink lists link image charmap print preview hr anchor pagebreak",
          "searchreplace wordcount visualblocks visualchars code fullscreen",
          "insertdatetime media nonbreaking save table contextmenu directionality",
          "template paste textcolor colorpicker textpattern imagetools toc help emoticons hr codesample",
        ];
      },
      type: Array,
    },
    toolbar: {
      default:
        "undo redo | formatselect | bold italic forecolor backcolor | link | alignleft aligncenter alignright alignjustify | numlist bullist outdent indent | removeformat | help",
      type: String,
    },
    options: {
      default: function () {
        return {};
      },
      type: Object,
    },
    readonly: {
      default: false,
      type: Boolean,
    },
  },
  watch: {
    value: function (newValue) {
      if (!this.isTyping) {
        if (this.editor !== null) this.editor.setContent(newValue);
        else this.content = newValue;
      }
    },
    readonly(value) {
      if (value) {
        this.editor.setMode("readonly");
      } else {
        this.editor.setMode("design");
      }
    },
  },
  beforeDestroy() {
    this.editor.destroy();
  },
  mounted() {
    this.content = this.value;
    this.init();
  },
};
</script>