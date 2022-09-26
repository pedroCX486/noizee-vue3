<script setup lang="ts">
import { inject, onBeforeMount, ref } from "vue";
import { QuillEditor } from "@vueup/vue-quill";
import { CloseThick } from "mdue";

const emitter: any = inject("emitter");

let showSaved = ref(false);
let editorTitle = ref("");
let editorContent = ref("Your ideas.");

let debounce = ref();
let quill = ref();

const createDebounce = () => {
  let timeout = null;
  return function (fnc: Function, delayMs?: number) {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      fnc();
    }, delayMs || 400);
  };
};

const closeModal = () => {
  emitter.emit('closeModal');
};

const storageSave = () => {
  showSaved.value = true;
  localStorage.setItem(
    "noizeeTextEditor",
    JSON.stringify({ title: editorTitle.value, content: editorContent.value })
  );
  setTimeout(() => {
    showSaved.value = false;
  }, 1000);
};

const storageLoad = () => {
  if (!!localStorage.getItem("noizeeTextEditor")) {
    let storageData = JSON.parse(localStorage.getItem("noizeeTextEditor"));
    editorTitle.value = storageData.title;
    editorContent.value = storageData.content;
  }
};

const printFromEditor = () => {
  const printWindow = window.open("", "Noizee", "height=720,width=1280");
  printWindow.document.write(
    "<html><head><title>" + editorTitle.value + "</title>"
  );
  printWindow.document.write("</head><body >");
  printWindow.document.write("<h1>" + editorTitle.value + "</h1>");
  printWindow.document.write(editorContent.value);
  printWindow.document.write("</body></html>");
  printWindow.document.close();
  printWindow.focus();
  printWindow.print();
  printWindow.close();
};

const resetEditor = () => {
  editorTitle.value = "";
  editorContent.value = "Your new ideas.";
  quill.value.setHTML(editorContent.value);
  storageSave();
};

onBeforeMount(() => {
  storageLoad();
  debounce.value = createDebounce();
});

</script>
    
<template>
  <div class="modal">
    <div class="modal-header">
      <div class="modal-title">Text Editor</div>
      <div>
        <close-thick class="md-normal-icon-size btn-animated btn-close" @click="closeModal()">
        </close-thick>
      </div>
    </div>
    <div class="modal-editor-title">
      <input v-model="editorTitle" type="text" class="editor-title" placeholder="Your title" @keydown="
        debounce(() => {
          storageSave();
        })
      " />
    </div>
    <div class="modal-editor">
      <QuillEditor v-model:content="editorContent" contentType="html" ref="quill" @keydown="
        debounce(() => {
          storageSave();
        })
      " theme="snow" />
    </div>
    <div class="modal-footer">
      <button class="custom-btn btn-info" @click="printFromEditor()">
        Print to File/Printer
      </button>
      &nbsp;
      <button class="custom-btn btn-danger" @click="resetEditor()">
        Reset Editor
      </button>
      <br />
      <small>Content autosaves to the browser's local storage.</small>
      &nbsp;
      <div class="powered-by">
        <small>
          <Transition>
            <span class="saved-hint" v-if="showSaved">Contents saved. &nbsp;</span>
          </Transition>
          Powered by
          <a href="https://vueup.github.io/vue-quill/">QuillEditor</a>.
        </small>
      </div>
    </div>
  </div>
</template>
    
<style scoped>
@import "./../assets/base.css";

.modal {
  position: absolute;
  left: 0;
  right: 0;
  margin: 0 auto;
  height: 60%;
  width: 80%;
  z-index: 2;
  background-color: white;
  border-radius: 5px;
  margin-top: 2rem;
}

.modal-header {
  height: 3rem;
  display: flex;
  flex-direction: row;
  align-content: center;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem;
  color: black;
  border-bottom: solid 1px lightgray;
}

.btn-close:hover {
  color: crimson;
  transition: ease-in 300ms;
  cursor: pointer;
}

.modal-title {
  font-size: x-large;
  font-weight: 600;
}

.modal-editor-title {
  width: 100%;
}

.editor-title {
  border: none;
  padding: 0.5rem;
  font-size: x-large;
  width: -webkit-fill-available;
}

.modal-editor {
  height: 24rem;
  background: white;
  overflow: hidden;
}

.modal-footer {
  background-color: white;
  border-bottom-left-radius: 5px;
  border-bottom-right-radius: 5px;
  padding-right: 0.5rem;
  padding-top: 3rem;
  padding-bottom: 0;
  padding-left: 1rem;
  color: black;
}

.powered-by {
  text-align: right;
  position: relative;
  bottom: 1rem;
  right: 1rem;
}

.saved-hint {
  color: rgb(202, 202, 202);
}
</style>
    